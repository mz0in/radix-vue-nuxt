<script setup lang="ts">
import MenuContentImpl, {
  type MenuContentImplEmits,
  type MenuRootContentProps,
} from './MenuContentImpl.vue'
import { injectMenuContext } from './MenuRoot.vue'
import { useEmitAsProps } from '@/shared'

const props = defineProps<MenuRootContentNonModalProps>()

const emits = defineEmits<MenuRootContentModalEmits>()

const menuContext = injectMenuContext()

interface MenuRootContentNonModalProps extends MenuRootContentProps {}
type MenuRootContentModalEmits = MenuContentImplEmits

const emitsAsProps = useEmitAsProps(emits)
</script>

<template>
  <MenuContentImpl
    v-bind="{ ...props, ...emitsAsProps }"
    :trap-focus="false"
    :disable-outside-pointer-events="false"
    :disable-outside-scroll="false"
    @dismiss="menuContext.onOpenChange(false)"
  >
    <slot />
  </MenuContentImpl>
</template>
