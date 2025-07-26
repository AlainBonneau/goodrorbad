<template>
  <div class="good-bad-bg">
    <div class="center-content">
      <h2>Bonne ou Mauvaise chance ?</h2>
      <input
        v-model="name"
        placeholder="Entre ton prénom"
        @keyup.enter="handleClick"
      />
      <button @click="handleClick">Tenter ma chance</button>

      <!-- Compteur chance/malchance -->
      <div class="compteurs">
        <span class="good-count">{{ goodCount }}</span>
        <span class="sep">|</span>
        <span class="bad-count">{{ badCount }}</span>
      </div>

      <div
        v-if="result"
        class="card-flip-container"
        :class="{ flipping: flipping }"
        :key="flipKey"
        @animationend="flipping = false"
      >
        <div class="card-flip-inner" :class="{ flipped: flipping }">
          <div
            class="card-flip-front card"
            :class="{ good: isGood, bad: isBad }"
          >
            ?
          </div>
          <div
            class="card-flip-back card"
            :class="{ good: isGood, bad: isBad }"
          >
            {{ result }}
          </div>
        </div>
      </div>
    </div>
    <div class="separator"></div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import messages from "../assets/messages.json";

const name = ref("");
const result = ref("");
const isGood = ref(false);
const isBad = ref(false);
const flipping = ref(false);
const flipKey = ref(0);

const goodCount = ref(0);
const badCount = ref(0);

const handleClick = () => {
  if (!name.value.trim()) {
    alert("Entre ton prénom d'abord patate !");
    isGood.value = false;
    isBad.value = false;
    flipping.value = false;
    flipKey.value++;
    return;
  }
  const good = Math.random() < 0.5;
  isGood.value = good;
  isBad.value = !good;
  const arr = good ? messages.good : messages.bad;
  const msg = arr[Math.floor(Math.random() * arr.length)];
  result.value = `${name.value.trim()}, ${msg}`;

  flipping.value = false;
  flipKey.value++;
  setTimeout(() => {
    flipping.value = true;
  }, 20);

  if (good) goodCount.value++;
  else badCount.value++;
};
</script>

<style scoped>
.good-bad-bg {
  position: relative;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(to right, #246bfd 0 50%, #e63946 50% 100%);
}
.separator {
  position: absolute;
  top: 0;
  left: 50%;
  width: 3px;
  height: 100%;
  background: #111;
  z-index: 1;
  transform: translateX(-50%);
  box-shadow: 0 0 8px #0007;
}
.center-content {
  position: relative;
  z-index: 2;
  background: #fff;
  border-radius: 2rem;
  box-shadow: 0 2px 28px #0002;
  padding: 2.7rem 3.2rem;
  display: flex;
  flex-direction: column;
  gap: 1.3rem;
  align-items: center;
  min-width: 380px;
  max-width: 98vw;
  width: 50%;
}

input,
button {
  padding: 0.7rem 1.3rem;
  border-radius: 10px;
  border: 1px solid #ccc;
  font-size: 1.08rem;
}
button {
  background: #246bfd;
  color: #fff;
  cursor: pointer;
  border: none;
  font-weight: bold;
  transition: background 0.15s;
}
button:hover {
  background: #194ba0;
}

/* Compteurs */
.compteurs {
  display: flex;
  align-items: center;
  font-size: 1.17rem;
  font-weight: bold;
  gap: 0.5rem;
  margin-bottom: 0.6rem;
}
.good-count {
  color: #246bfd;
  font-size: 1.5rem;
  padding: 0 7px;
}
.bad-count {
  color: #e63946;
  font-size: 1.5rem;
  padding: 0 7px;
}
.sep {
  color: #444;
  font-size: 1.22rem;
}

/* Carte */
.card-flip-container {
  perspective: 1100px;
  width: 85%;
  height: 155px;
  margin-top: 2rem;
  max-width: 100vw;
}
.card-flip-inner {
  width: 100%;
  height: 100%;
  position: relative;
  transition: transform 0.8s cubic-bezier(0.63, 0.04, 0.27, 1.28);
  transform-style: preserve-3d;
}
.card-flip-inner.flipped {
  transform: rotateY(180deg);
}
.card-flip-front,
.card-flip-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 1.35rem;
  font-weight: bold;
  padding: 2.1rem;
  border-radius: 1.6rem;
  background: #fff;
  box-shadow: 0 4px 32px #0002;
  border: 2.3px solid #999;
}
.card-flip-front {
  border-color: #999;
  color: #bbb;
  font-size: 2.2rem;
  letter-spacing: 2px;
  background: #f5f6fa;
}
.card-flip-back {
  transform: rotateY(180deg);
  border-color: #246bfd;
}
.card-flip-back.good {
  border-color: #246bfd;
  color: #246bfd;
}
.card-flip-back.bad {
  border-color: #e63946;
  color: #e63946;
}
.card-flip-front.good {
  border-color: #246bfd;
}
.card-flip-front.bad {
  border-color: #e63946;
}

/* Responsive mobile */
@media (max-width: 600px) {
  .center-content {
    min-width: 95vw;
    width: 98vw;
    padding: 1.1rem 0.2rem;
    border-radius: 1rem;
  }
  .card-flip-container {
    width: 98vw;
    min-width: unset;
    height: 115px;
  }
  .card-flip-front,
  .card-flip-back {
    font-size: 1.03rem;
    padding: 1.1rem;
    border-radius: 0.9rem;
  }
  h2 {
    font-size: 1.15rem;
    text-align: center;
  }
  input,
  button {
    font-size: 0.99rem;
    padding: 0.6rem 0.7rem;
  }
  .compteurs {
    font-size: 1.07rem;
  }
}
</style>
