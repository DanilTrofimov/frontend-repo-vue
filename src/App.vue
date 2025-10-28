<template>
  <div class="h-screen grid place-items-center">
    <div class="flex flex-col gap-15">
      <NumericInput 
        v-model="age"
        label="Samuel is"
        caption="cat years old"
        name="age"
        thumb="./src/assets/samuel.jpg"
      /> 
      <NumericInput 
        v-model="weight"
        label="Christophe is"
        caption="kg"
        name="weight"
        thumb="./src/assets/christophe.jpeg"
      />
      <NumericInput 
        v-model="height"
        label="Rose is"
        caption="cm tall"
        name="height"
        thumb="./src/assets/rose.jpeg"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import NumericInput from './NumericInput.vue'

const age = ref<number | null>(null)
const weight = ref<number | null>(null)
const height = ref<number | null>(null)
</script>
