<script lang="ts">
import { computed } from 'vue'

export interface ToggleProps extends PrimitiveProps {
  /**
   * The pressed state of the toggle when it is initially rendered. Use when you do not need to control its open state.
   */
  defaultValue?: boolean

  /**
   * The controlled pressed state of the toggle.
   */
  pressed?: boolean
  /**
   * Disables the trigger.
   */
  disabled?: boolean
}
export type ToggleEmits = {
  'update:pressed': [value: boolean]
}

export type DataState = 'on' | 'off'
</script>

<script setup lang="ts">
import { useVModel } from '@vueuse/core'
import { Primitive, type PrimitiveProps } from '@/Primitive'

const props = withDefaults(defineProps<ToggleProps>(), {
  pressed: undefined,
  disabled: false,
  as: 'button',
})

const emits = defineEmits<ToggleEmits>()

const pressed = useVModel(props, 'pressed', emits, {
  defaultValue: props.defaultValue,
  passive: (props.pressed === undefined) as false,
})

function togglePressed() {
  pressed.value = !pressed.value
}

const dataState = computed<DataState>(() => {
  return pressed.value ? 'on' : 'off'
})
</script>

<template>
  <Primitive
    :type="as === 'button' ? 'button' : undefined"
    :as-child="props.asChild"
    :as="as"
    :aria-pressed="pressed"
    :data-state="dataState"
    :data-disabled="disabled ? '' : undefined"
    :disabled="disabled"
    @click="togglePressed"
    @keydown.enter="togglePressed"
  >
    <slot />
  </Primitive>
</template>
