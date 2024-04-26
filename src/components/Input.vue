<script setup>
import { onMounted, ref, reactive, defineProps } from "vue";

const text = ref("");
const emits = defineEmits(["onNewItem"]);

function handleKeyDown(evt) {
  if (evt.key === "Enter") {
    emits("onNewItem", text.value);
    text.value = "";
  }
}

</script>

<template><input v-model="text" @keydown="handleKeyDown" />{{ text }}</template>

<style scoped></style>
