<template>
  <div class="p-5">
    <div class="mb-2">
      <p>Инпуты связаны переменной, компонент позволяет редактирование значения и не падает если в нем появятся символы
      </p>
      <p>Переменная: {{ number }}</p>
    </div>
    <div class="mb-1">
      <label for="basic-input">Обычный input:</label>
      <input type="text" id="basic-input" v-model="number">
    </div>
    <label for="basic-input">Кастомный input:</label>
    <NumericInput v-model="number" />
  </div>
</template>

<script setup lang="ts">
import NumericInput from "@/components/NumericInput.vue"
import { ref } from "vue";
const number = ref()
</script>

<style scoped>
input {
  margin-bottom: 1rem;
}

label {
  display: block;
}
</style>
