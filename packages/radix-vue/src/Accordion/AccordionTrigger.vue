<script setup lang="ts">
import { injectAccordionItemContext } from './AccordionItem.vue'
import { injectAccordionRootContext } from './AccordionRoot.vue'
import { type PrimitiveProps } from '@/Primitive'
import { CollapsibleTrigger } from '@/Collapsible'

export interface AccordionTriggerProps extends PrimitiveProps {}

const props = defineProps<AccordionTriggerProps>()

const rootContext = injectAccordionRootContext()
const itemContext = injectAccordionItemContext()

function changeItem() {
  if (itemContext.disabled.value)
    return
  rootContext.changeModelValue(itemContext.value.value)
}
</script>

<template>
  <CollapsibleTrigger
    :id="itemContext.triggerId"
    :ref="itemContext.primitiveElement"
    data-radix-vue-collection-item
    :as="props.as"
    :as-child="props.asChild"
    :aria-controls="itemContext.triggerId"
    :aria-disabled="itemContext.disabled.value || undefined"
    :aria-expanded="itemContext.open.value || false"
    :data-disabled="itemContext.dataDisabled.value"
    :data-orientation="rootContext.orientation"
    :data-state="itemContext.dataState.value"
    :disabled="itemContext.disabled.value"
    @click="changeItem"
  >
    <slot />
  </CollapsibleTrigger>
</template>
