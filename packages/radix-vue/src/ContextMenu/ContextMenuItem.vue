<script setup lang="ts">
import { MenuItem, type MenuItemEmits, type MenuItemProps } from '@/Menu'
import { useEmitAsProps } from '@/shared'

export interface ContextMenuItemProps extends MenuItemProps {}
export type ContextMenuItemEmits = MenuItemEmits

const props = defineProps<MenuItemProps>()
const emits = defineEmits<MenuItemEmits>()

const emitsAsProps = useEmitAsProps(emits)
</script>

<template>
  <MenuItem v-bind="{ ...props, ...emitsAsProps }">
    <slot />
  </MenuItem>
</template>
