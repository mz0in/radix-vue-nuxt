<script setup lang="ts">
import { onBeforeMount, onMounted, useSlots } from 'vue'
import { shouldShowPlaceholder } from './utils'
import { injectSelectRootContext } from './SelectRoot.vue'
import {
  Primitive,
  type PrimitiveProps,
  usePrimitiveElement,
} from '@/Primitive'
import { renderSlotFragments } from '@/shared'

export interface SelectValueProps extends PrimitiveProps {
  placeholder?: string
}

withDefaults(defineProps<SelectValueProps>(), {
  as: 'span',
  placeholder: '',
})

const { primitiveElement, currentElement } = usePrimitiveElement()

const rootContext = injectSelectRootContext()
const slots = useSlots()

onBeforeMount(() => {
  const hasChildren = !!renderSlotFragments(slots?.default?.()).length
  rootContext.onValueElementHasChildrenChange(hasChildren)
})

onMounted(() => {
  rootContext.valueElement = currentElement
})
</script>

<template>
  <Primitive
    ref="primitiveElement"
    :as="as"
    :as-child="asChild"
    :style="{ pointerEvents: 'none' }"
  >
    <template v-if="shouldShowPlaceholder(rootContext.modelValue?.value)">
      {{ placeholder }}
    </template>

    <template v-else>
      <slot />
    </template>
  </Primitive>
</template>
