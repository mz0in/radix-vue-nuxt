<script setup lang="ts">
import { onMounted } from 'vue'
import { injectNavigationMenuContext } from './NavigationMenuRoot.vue'
import {
  Primitive,
  type PrimitiveProps,
  usePrimitiveElement,
} from '@/Primitive'

export interface NavigationMenuListProps extends PrimitiveProps {}
const props = withDefaults(defineProps<NavigationMenuListProps>(), {
  as: 'ul',
})

const menuContext = injectNavigationMenuContext()
const { primitiveElement, currentElement } = usePrimitiveElement()

onMounted(() => {
  menuContext.onIndicatorTrackChange(currentElement.value)
})
</script>

<script lang="ts">
export default {
  inheritAttrs: false,
}
</script>

<template>
  <Primitive ref="primitiveElement" style="position: relative">
    <Primitive
      v-bind="$attrs"
      :as-child="props.asChild"
      :as="as"
      :data-orientation="menuContext.orientation"
    >
      <slot />
    </Primitive>
  </Primitive>
</template>
