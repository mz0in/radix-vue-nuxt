<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { useDebounceFn, useResizeObserver } from '@vueuse/core'
import { injectScrollAreaRootContext } from './ScrollAreaRoot.vue'
import { injectScrollAreaScrollbarContext } from './ScrollAreaScrollbar.vue'
import ScrollAreaScrollbarVisible from './ScrollAreaScrollbarVisible.vue'
import { Presence } from '@/Presence'
import { useForwardRef } from '@/shared'

export interface ScrollAreaScrollbarAutoProps {
  forceMount?: boolean
}
defineProps<ScrollAreaScrollbarAutoProps>()

const rootContext = injectScrollAreaRootContext()
const scrollbarContext = injectScrollAreaScrollbarContext()

const forwardRef = useForwardRef()

const visible = ref(false)

const handleResize = useDebounceFn(() => {
  if (rootContext.viewport.value) {
    const isOverflowX
      = rootContext.viewport.value.offsetWidth
      < rootContext.viewport.value.scrollWidth
    const isOverflowY
      = rootContext.viewport.value.offsetHeight
      < rootContext.viewport.value.scrollHeight

    visible.value = scrollbarContext.isHorizontal.value
      ? isOverflowX
      : isOverflowY
  }
}, 10)

onMounted(() => handleResize())

useResizeObserver(rootContext.viewport, handleResize)
useResizeObserver(rootContext.content, handleResize)
</script>

<template>
  <Presence :present="forceMount || visible">
    <ScrollAreaScrollbarVisible
      v-bind="$attrs"
      ref="forwardRef"
      :data-state="visible ? 'visible' : 'hidden'"
    >
      <slot />
    </ScrollAreaScrollbarVisible>
  </Presence>
</template>
