<script setup lang="ts">
defineProps<{
  label: string;
  name: string;
  hint?: string;
}>();
</script>

<template>
  <div class="mb-4">
    <div class="flex justify-between">
      <label class="text-sm font-semibold" :for="name">
        {{ label }}
      </label>
      <ErrorMessage :name="name" class="text-error" />
    </div>
    <div class="w-full my-2 min-h-[44px] flex align-stretch">
      <slot></slot>
    </div>
    <p v-if="hint" class="text-xs text-gray-400">
      {{ hint }}
    </p>
  </div>
</template>
