<script lang="ts">
import { Primitive, type PrimitiveProps } from '@/Primitive'

export interface ComboboxLabelProps extends PrimitiveProps {
  for?: string
}
</script>

<script setup lang="ts">
import { injectComboboxGroupContext } from './ComboboxGroup.vue'

const props = withDefaults(defineProps<ComboboxLabelProps>(), {
  as: 'label',
})

const groupContext = injectComboboxGroupContext({ id: '' })
</script>

<template>
  <Primitive v-bind="props" :id="groupContext.id">
    <slot />
  </Primitive>
</template>
