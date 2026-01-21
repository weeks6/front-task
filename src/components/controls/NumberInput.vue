<script setup lang="ts">
import { computed, nextTick, onMounted, ref, useTemplateRef, watch } from "vue";
import { useIMask } from "vue-imask";

const props = defineProps<{
  label?: string;
  caption?: string;
  photo?: string;
  photoAlt?: string;
  modelValue: number;
}>();

const emit = defineEmits<{
  (event: "update:modelValue", value: number): void;
}>();

const mirrorRef = useTemplateRef("mirrorRef");

const { el: inputRef, masked: maskedValue } = useIMask(
  {
    mask: Number,
    thousandsSeparator: " ",
    min: 0,
  },
  {
    defaultValue: props.modelValue.toString(),
    emit(t, value) {
      if (t === "accept:unmasked") {
        emit("update:modelValue", Number(value));
      }
    },
  },
);

// since it relies on the font there's a bug if font isnt ready
async function applyWidth() {
  await nextTick();
  if (!inputRef.value || !mirrorRef.value) return;

  (inputRef.value as HTMLInputElement).style.width = mirrorRef.value.offsetWidth + "px";
}

watch(maskedValue, () => {
  applyWidth();
});

onMounted(() => {
  applyWidth();
});

const isFocused = ref(false);
function handleFocus() {
  isFocused.value = true;
}

function handleBlur() {
  isFocused.value = false;
}
</script>

<template>
  <div class="flex items-center gap-4">
    <img
      v-if="photo"
      src="/img.png"
      :alt="photoAlt || label || caption"
      class="w-20 h-20 rounded-full object-cover transition-shadow"
      :class="{
        photo: isFocused,
      }"
    />
    <div class="flex flex-col gap-3">
      <label
        for="number-input"
        class="block text-[16px] koulen-regular tracking-wide transition-colors"
        :class="{
          ' text-(--c-dark)': !isFocused,
          'text-(--c-primary)': isFocused,
        }"
      >
        {{ label }}
      </label>
      <div class="relative flex items-center gap-3">
        <div class="relative inline-block min-w-18">
          <span
            ref="mirrorRef"
            class="invisible inter-500 border absolute whitespace-pre px-2 py-[11px] text-[18px] leading-none"
          >
            {{ maskedValue || "0" }}
          </span>
          <input
            ref="inputRef"
            id="number-input"
            type="text"
            inputmode="numeric"
            class="text-(--c-dark) min-w-18 px-2 py-[11px] h-[44px] text-[18px] leading-none inter-500 border border-gray-300 transition-colors rounded outline-none focus-within:border-(--c-primary-light) caret-(--c-primary-light)"
            placeholder="0"
            @focus="handleFocus"
            @blur="handleBlur"
          />
        </div>
        <span class="inter-400 text-[18px] text-(--c-dark)">{{ caption }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.photo {
  box-shadow:
    0 0 0 3px var(--color-white),
    0 0 0 4px var(--c-primary);
}
</style>
