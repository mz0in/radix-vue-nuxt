<script lang="ts">
import type { Ref } from 'vue'
import { useVModel } from '@vueuse/core'
import { createContext, useFormControl } from '@/shared'

export interface CheckboxRootProps extends PrimitiveProps {
  defaultChecked?: boolean
  checked?: boolean | 'indeterminate'
  disabled?: boolean
  required?: boolean
  name?: string
  value?: string
  id?: string
}

export type CheckboxRootEmits = {
  'update:checked': [value: boolean]
}

interface CheckboxRootContext {
  disabled: Ref<boolean>
  state: Ref<CheckedState>
}

export const [injectCheckboxRootContext, provideCheckboxRootContext]
  = createContext<CheckboxRootContext>('CheckboxRoot')

export default {
  inheritAttrs: false,
}
</script>

<script setup lang="ts">
import { computed, toRefs } from 'vue'
import { Primitive, type PrimitiveProps, usePrimitiveElement } from '@/Primitive'
import type { CheckedState } from './utils'
import { getState, isIndeterminate } from './utils'

const props = withDefaults(defineProps<CheckboxRootProps>(), {
  checked: undefined,
  value: 'on',
  as: 'button',
})
const emits = defineEmits<CheckboxRootEmits>()
const { disabled } = toRefs(props)

const checked = useVModel(props, 'checked', emits, {
  defaultValue: props.defaultChecked,
  passive: (props.checked === undefined) as false,
}) as Ref<CheckedState>

const { primitiveElement, currentElement } = usePrimitiveElement()
const isFormControl = useFormControl(currentElement)
const ariaLabel = computed(() => props.id && currentElement.value
  ? (document.querySelector(`[for="${props.id}"]`) as HTMLLabelElement)?.innerText
  : undefined)

provideCheckboxRootContext({
  disabled,
  state: checked,
})
</script>

<template>
  <Primitive
    v-bind="$attrs"
    :id="id"
    ref="primitiveElement"
    role="checkbox"
    :as-child="props.asChild"
    :as="as"
    :type="as === 'button' ? 'button' : undefined"
    :aria-checked="isIndeterminate(checked) ? 'mixed' : checked"
    :aria-required="false"
    :aria-label="$attrs['aria-label'] || ariaLabel"
    :data-state="getState(checked)"
    :data-disabled="disabled ? '' : undefined"
    :disabled="disabled"
    @keydown.enter.prevent="() => {
      // According to WAI ARIA, Checkboxes don't activate on enter keypress
    }"
    @click="checked = !checked"
  >
    <slot />
  </Primitive>

  <input
    v-if="isFormControl"
    type="checkbox"
    tabindex="-1"
    aria-hidden
    :value="value"
    :checked="!!checked"
    :name="props.name"
    :disabled="props.disabled"
    :required="props.required"
    :style="{
      transform: 'translateX(-100%)',
      position: 'absolute',
      pointerEvents: 'none',
      opacity: 0,
      margin: 0,
    }"
  >
</template>
