<script lang="ts">
import {
  createContext,
  useBodyScrollLock,
  useHideOthers,
} from '@/shared'

export type ComboboxContentImplEmits = {
  closeAutoFocus: [event: Event]
  /**
   * Event handler called when the escape key is down.
   * Can be prevented.
   */
  escapeKeyDown: [event: KeyboardEvent]
  /**
   * Event handler called when the a `pointerdown` event happens outside of the `DismissableLayer`.
   * Can be prevented.
   */
  pointerDownOutside: [event: PointerDownOutsideEvent]
}

export interface ComboboxContentImplProps extends PopperContentProps {
  position?: 'inline' | 'popper'
  bodyLock?: boolean
  disableOutsidePointerEvents?: boolean
}

export const [injectComboboxContentContext, provideComboboxContentContext]
  = createContext<{
    position: Ref<'inline' | 'popper'>
  }>('ComboboxContent')
</script>

<script setup lang="ts">
import {
  type Ref,
  computed,
  onMounted,
  toRefs,
} from 'vue'
import { injectComboboxRootContext } from './ComboboxRoot.vue'
import {
  DismissableLayer,
  type PointerDownOutsideEvent,
} from '@/DismissableLayer'
import { PopperContent, type PopperContentProps } from '@/Popper'
import { Primitive, usePrimitiveElement } from '@/Primitive'

const props = withDefaults(defineProps<ComboboxContentImplProps>(), {
  position: 'inline',
})
const emits = defineEmits<ComboboxContentImplEmits>()

const { position } = toRefs(props)
const rootContext = injectComboboxRootContext()

useBodyScrollLock(props.bodyLock)

const { primitiveElement, currentElement } = usePrimitiveElement()
useHideOthers(currentElement)

const pickedProps = computed(() => {
  if (props.position === 'popper')
    return props
  else return {}
})

function handleLeave(ev: PointerEvent) {
  rootContext.onSelectedValueChange('')
}

onMounted(() => {
  rootContext.onContentElementChange(currentElement.value)
})

const popperStyle = {
  // Ensure border-box for floating-ui calculations
  'boxSizing': 'border-box',
  '--radix-combobox-content-transform-origin':
        'var(--radix-popper-transform-origin)',
  '--radix-combobox-content-available-width':
        'var(--radix-popper-available-width)',
  '--radix-combobox-content-available-height':
        'var(--radix-popper-available-height)',
  '--radix-combobox-trigger-width': 'var(--radix-popper-anchor-width)',
  '--radix-combobox-trigger-height': 'var(--radix-popper-anchor-height)',
}

provideComboboxContentContext({ position })
</script>

<template>
  <DismissableLayer
    as-child
    :disable-outside-pointer-events="disableOutsidePointerEvents"
    @focus-outside.prevent
    @dismiss="rootContext.onOpenChange(false)"
    @escape-key-down="emits('escapeKeyDown', $event)"
    @pointer-down-outside="(ev) => {
      // if clicking inside the combobox, prevent dismiss
      if (rootContext.parentElement.value?.contains(ev.target as Node)) ev.preventDefault()
      emits('pointerDownOutside', ev)
    }"
  >
    <component
      :is="position === 'popper' ? PopperContent : Primitive "
      v-bind="{ ...$attrs, ...pickedProps }"
      :id="rootContext.contentId"
      ref="primitiveElement"
      role="listbox"
      :data-state="rootContext.open.value ? 'open' : 'closed'"
      :style="{
        // flex layout so we can place the scroll buttons properly
        display: 'flex',
        flexDirection: 'column',
        // reset the outline by default as the content MAY get focused
        outline: 'none',
        ...(position === 'popper' ? popperStyle : {}),
      }"
      @pointerleave="handleLeave"
    >
      <slot />
    </component>
  </DismissableLayer>
</template>
