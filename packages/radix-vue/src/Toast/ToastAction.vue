<script setup lang="ts">
import ToastAnnounceExclude from './ToastAnnounceExclude.vue'
import ToastClose, { type ToastCloseProps } from './ToastClose.vue'

export interface ToastActionProps extends ToastCloseProps {
  /**
   * A short description for an alternate way to carry out the action. For screen reader users
   * who will not be able to navigate to the button easily/quickly.
   * @example <ToastAction altText="Goto account settings to upgrade">Upgrade</ToastAction>
   * @example <ToastAction altText="Undo (Alt+U)">Undo</ToastAction>
   */
  altText: string
}

const props = defineProps<ToastActionProps>()

if (!props.altText)
  throw new Error('Missing prop `altText` expected on `ToastAction`')
</script>

<template>
  <ToastAnnounceExclude v-if="altText" :alt-text="altText" as-chld>
    <ToastClose :as="as" :as-child="asChild">
      <slot />
    </ToastClose>
  </ToastAnnounceExclude>
</template>
