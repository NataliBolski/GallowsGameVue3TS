<template>
  <GameHeader />
  <div class="game-container">
    <GameFigure :wrongLettersCount="wrongLetters.length" />
    <GameWrongLetters :wrongLetters="wrongLetters" />
    <GameWord :word="word" :correctLetters="correctLetters" />
  </div>
  <GamePopup ref="popup" :word="word" @restart="restart" />
  <GameNotification ref="notification" />
</template>

<script setup lang="ts">
import GameHeader from "./components/GameHeader.vue";
import GameFigure from "./components/GameFigure.vue";
import GameWrongLetters from "./components/GameWrongLetters.vue";
import GameWord from "./components/GameWord.vue";
import GamePopup from "./components/GamePopup.vue";
import GameNotification from "./components/GameNotification.vue";
import { computed, ref, watch } from "vue";
import axios from "axios";

const getRandomWord = async () => {
  try {
    const { data } = await axios<{ FirstName: string }>(
      "https://api.randomdatatools.ru/?unescaped=false&params=FirstName"
    );
    word.value = data.FirstName.toLowerCase();
  } catch (err) {
    console.log(err);
    word.value = "";
  }
};

getRandomWord();

const word = ref("василий");
const letters = ref<string[]>([]);
const notification = ref<InstanceType<typeof GameNotification> | null>(null);
const popup = ref<InstanceType<typeof GamePopup> | null>(null);

const isLose = computed(() => wrongLetters.value.length === 6);
const isWin = computed(() =>
  word.value.split('').every(x => letters.value.includes(x))
);

const correctLetters = computed(() =>
  letters.value.filter((x) => word.value.includes(x))
);
const wrongLetters = computed(() =>
  letters.value.filter((x) => !word.value.includes(x))
);

watch(wrongLetters, () => {
  if (wrongLetters.value.length === 6) {
    popup.value?.open("lose");
  }
});

watch(isWin, (value) => {
  if (value) {
    popup.value?.open("win");
  }
});

window.addEventListener("keydown", ({ key }) => {
  if (isLose.value || isWin.value) {
    return;
  }
  if (letters.value.includes(key)) {
    notification.value?.open();
    setTimeout(() => notification.value?.close(), 2000);
    return;
  }
  if (/[а-яА-ЯёЁ]/.test(key)) {
    letters.value.push(key.toLowerCase());
  }
});

const restart = async() => {
  await getRandomWord();
  letters.value = [];
  popup.value?.close();
};
</script>
