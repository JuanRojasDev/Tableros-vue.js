<script>
import { ref } from 'vue';

export default {
  name: 'CreateDialog',
  props: ['show', 'message'],
  setup(props, { emit }) {
    const inputValue = ref('');

    const confirmAndClear = () => {
      emit('confirm', inputValue.value);
      inputValue.value = '';
    };

    const cancelAndClear = () => {
      emit('cancel');
      inputValue.value = '';
    };

    return {
      inputValue,
      confirmAndClear,
      cancelAndClear
    };
  }
}
</script>

<template>
  <div v-if="show" class="create-dialog-overlay">
    <div class="create-dialog">
      <p>{{ message }}</p>
      <input v-model="inputValue" type="text" class="create-dialog-input" placeholder="Escribe el nombre del tablero">
      <div class="create-dialog-buttons">
        <button class="create-dialog-button confirm" @click="confirmAndClear">Confirmar</button>
        <button class="create-dialog-button cancel" @click="cancelAndClear">Cancelar</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
@import "../assets/main.css";
</style>
