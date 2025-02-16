<script setup>
import { ref, computed } from "vue";

const timer = ref(null);
const seconds = ref(0);
const minutes = ref(0);
const hours = ref(0);
const running = ref(false);
const timerGifVisible = ref(false);
const sessions = ref([]);
const countdownMode = ref(false);
const countdownTarget = ref(0);

const totalTimeSpent = computed(() =>
  sessions.value.reduce((acc, session) => acc + session, 0)
);

const longestSession = computed(() =>
  sessions.value.length ? Math.max(...sessions.value) : 0
);

const shortestSession = computed(() =>
  sessions.value.length ? Math.min(...sessions.value) : 0
);

const formattedTime = computed(() => {
  let hrs = String(hours.value).padStart(2, "0");
  let mins = String(minutes.value).padStart(2, "0");
  let secs = String(seconds.value).padStart(2, "0");
  return `${hrs}:${mins}:${secs}`;
});

const updateTimer = () => {
  if (countdownMode.value) {
    if (seconds.value > 0 || minutes.value > 0 || hours.value > 0) {
      if (seconds.value === 0) {
        if (minutes.value > 0) {
          seconds.value = 59;
          minutes.value--;
        } else if (hours.value > 0) {
          minutes.value = 59;
          seconds.value = 59;
          hours.value--;
        }
      } else {
        seconds.value--;
      }
    } else {
      stopTimer();
      alert("Countdown finished!");
    }
  } else {
    seconds.value++;
    if (seconds.value === 60) {
      seconds.value = 0;
      minutes.value++;
    }
    if (minutes.value === 60) {
      minutes.value = 0;
      hours.value++;
    }
  }
};

const startPauseTimer = () => {
  if (!running.value) {
    running.value = true;
    timerGifVisible.value = true;
    timer.value = setInterval(updateTimer, 1000);
  } else {
    running.value = false;
    clearInterval(timer.value);
    timerGifVisible.value = false;
  }
};

const resetTimer = () => {
  running.value = false;
  clearInterval(timer.value);
  const sessionTime = hours.value * 3600 + minutes.value * 60 + seconds.value;
  if (sessionTime > 0) {
    sessions.value.push(sessionTime);
  }
  seconds.value = 0;
  minutes.value = 0;
  hours.value = 0;
  timerGifVisible.value = false;
};

const setCountdown = () => {
  const inputTime = prompt("Enter countdown time in minutes:", "5");
  if (inputTime !== null && !isNaN(inputTime)) {
    countdownMode.value = true;
    countdownTarget.value = parseInt(inputTime) * 60;
    hours.value = Math.floor(countdownTarget.value / 3600);
    minutes.value = Math.floor((countdownTarget.value % 3600) / 60);
    seconds.value = countdownTarget.value % 60;
  }
};
</script>

<template>
  <div class="container">
    <h1>{{ formattedTime }}</h1>
    <img v-show="timerGifVisible" src="/horseRun.gif" alt="Timer Animation">
    <br>
    <button @click="startPauseTimer">{{ running ? 'Pause' : 'Start' }}</button>
    <button @click="resetTimer">Reset</button>
    <button @click="setCountdown">Set Countdown</button>

    <div class="session-history">
      <h3>Session History</h3>
      <ul>
        <li v-for="(session, index) in sessions" :key="index">
          Session {{ index + 1 }}: {{ Math.floor(session / 60) }} min {{ session % 60 }} sec
        </li>
      </ul>
      <p>Total Time Spent: {{ Math.floor(totalTimeSpent / 60) }} min {{ totalTimeSpent % 60 }} sec</p>
      <p>Longest Session: {{ Math.floor(longestSession / 60) }} min {{ longestSession % 60 }} sec</p>
      <p>Shortest Session: {{ Math.floor(shortestSession / 60) }} min {{ shortestSession % 60 }} sec</p>
    </div>
  </div>
</template>
