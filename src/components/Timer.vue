<script setup>
import { ref, computed } from "vue";

const timer = ref(null);
const seconds = ref(0);
const minutes = ref(0);
const hours = ref(0);
const running = ref(false);
const timerGifVisible = ref(false);

const formattedTime = computed(() => {
  let hrs = String(hours.value).padStart(2, "0");
  let mins = String(minutes.value).padStart(2, "0");
  let secs = String(seconds.value).padStart(2, "0");
  return `${hrs}:${mins}:${secs}`;
});

const updateTimer = () => {
  seconds.value++;
  if (seconds.value === 60) {
    seconds.value = 0;
    minutes.value++;
  }
  if (minutes.value === 60) {
    minutes.value = 0;
    hours.value++;
  }
};

const startTimer = () => {
  if (!running.value) {
    running.value = true;
    timerGifVisible.value = true;
    timer.value = setInterval(updateTimer, 1000);
  }
};

const stopTimer = () => {
  running.value = false;
  clearInterval(timer.value);
  timerGifVisible.value = false;
};

const resetTimer = () => {
  running.value = false;
  clearInterval(timer.value);
  seconds.value = 0;
  minutes.value = 0;
  hours.value = 0;
  timerGifVisible.value = false;
};
</script>

<template>
  <div class="container">
    <h1>{{ formattedTime }}</h1>
    <img v-show="timerGifVisible" src="/horseRun.gif" alt="Timer Animation">
    <br>
    <button @click="startTimer">Start</button>
    <button @click="stopTimer">Stop</button>
    <button @click="resetTimer">Reset</button>
  </div>
</template>

<style scoped>
.container {
  text-align: center;
}

h1 {
  font-size: 3rem;
  margin-bottom: 50px;
}

button {
  font-size: 1rem;
  padding: 10px 20px;
  margin: 5px;
  border: none;
  background-color: #ff00cc;
  color: white;
  cursor: pointer;
  border-radius: 5px;
}

button:hover {
  background-color: #0056b3;
}

#timerGif {
  width: auto;
  height: auto;
  margin-left: 10px;
  vertical-align: middle;
}

.hidden {
  display: none;
}
</style>
