<template>
  <div class="app">
    <NumericInput imgSrc="./photo.png" label="my label" caption="my caption" v-model="number" />
  </div>
</template>

<script setup lang="ts">
import NumericInput from "@/components/NumericInput.vue"
import { ref } from "vue";
const number = ref()
</script>

<style scoped>
.app {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
