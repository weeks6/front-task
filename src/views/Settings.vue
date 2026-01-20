<script setup lang="ts">
import { store } from "@/store";

import NumberInput from "@/components/controls/NumberInput.vue";

function updateMinimumAge(value: number) {
  store.minimumAgeInMonths = value ?? 0;
}
</script>

<template>
  <div class="flex flex-col gap-4">
    <router-link to="/" class="text-violet-600 hover:underline text-sm">&larr; Back</router-link>

    <h1 class="text-xl font-bold text-gray-700">Settings</h1>

    <number-input
      label="MINIMUM AGE"
      caption="months"
      :modelValue="store.minimumAgeInMonths"
      @update:modelValue="updateMinimumAge"
    />
  </div>
</template>
