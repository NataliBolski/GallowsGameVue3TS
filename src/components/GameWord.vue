<template>
  <div class="word">
    <span v-for="(letter, index) in word" :key="index" class="letter">
      {{ correctLetters.includes(word[index]) ? word[index] : '' }}
    </span>
  </div>
</template>

<script setup lang="ts">
import { defineProps } from 'vue';
interface Props {
  word: string
  correctLetters: string[]
}

defineProps<Props>()
</script>
