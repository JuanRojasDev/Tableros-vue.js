<template>
  <div v-if="show" class="confirm-dialog-overlay">
    <div class="confirm-dialog">
      <p>{{ message }}</p>
      <div class="confirm-dialog-buttons">
        <button class="confirm-dialog-button confirm" @click="$emit('confirm')">Confirmar</button>
        <button class="confirm-dialog-button cancel" @click="$emit('cancel')">Cancelar</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["show", "message"],
};
</script>

<style scoped>
@import "../assets/main.css";
</style>
