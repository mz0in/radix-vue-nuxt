<script lang="ts">
import { createContext, useId } from '@/shared'

export interface SelectGroupProps extends PrimitiveProps {}

interface SelectGroupContext {
  id: string
}

export const [injectSelectGroupContext, provideSelectGroupContext]
  = createContext<SelectGroupContext>('SelectGroup')
</script>

<script setup lang="ts">
import { Primitive, type PrimitiveProps } from '@/Primitive'

const props = defineProps<SelectGroupProps>()

const id = useId()
provideSelectGroupContext({ id })
</script>

<template>
  <Primitive role="group" v-bind="props" :aria-labelledby="id">
    <slot />
  </Primitive>
</template>
