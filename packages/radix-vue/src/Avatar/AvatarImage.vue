<script lang="ts">
export type AvatarImageEmits = {
  'loadingStatusChange': [value: ImageLoadingStatus]
}
export interface AvatarImageProps extends PrimitiveProps {
  src: string
}
</script>

<script setup lang="ts">
import { toRefs, watch } from 'vue'
import { Primitive, type PrimitiveProps } from '../Primitive'
import { injectAvatarRootContext } from './AvatarRoot.vue'
import { type ImageLoadingStatus, useImageLoadingStatus } from './utils'

const props = withDefaults(defineProps<AvatarImageProps>(), { as: 'img' })
const emits = defineEmits<AvatarImageEmits>()

const { src } = toRefs(props)
const rootContext = injectAvatarRootContext()

const imageLoadingStatus = useImageLoadingStatus(src)

watch(
  imageLoadingStatus,
  (newValue) => {
    emits('loadingStatusChange', newValue)
    if (newValue !== 'idle')
      rootContext.imageLoadingStatus.value = newValue
  },
  { immediate: true },
)
</script>

<template>
  <Primitive
    v-if="imageLoadingStatus === 'loaded'"
    role="img"
    :as-child="asChild"
    :as="as"
    :src="src"
  >
    <slot />
  </Primitive>
</template>
