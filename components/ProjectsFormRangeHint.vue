<script setup lang="ts">
defineProps<{
  amounts: number[];
}>();
</script>

<template>
  <div class="flex justify-between w-full px-2 text-xs">
    <span v-for="index in amounts.length" :key="index">
      |
    </span>
  </div>
  <div class="flex justify-between w-full px-2 text-xs">
    <span v-for="amount in amounts" :key="amount">
      <Money :amount="amount" />
    </span>
  </div>
</template>
