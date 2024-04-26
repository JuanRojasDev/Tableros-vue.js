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

<template>
  <input
    v-model="text"
    @keydown="handleKeyDown"
    placeholder="Escriba su apunte aquí"
  />{{ text }}
</template>

<style scoped>
@import "../assets/main.css";
</style>
