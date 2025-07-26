<template>
  <div class="good-bad-bg">
    <div class="center-content">
      <h2>Bonne ou Mauvaise chance ?</h2>
      <p class="subtitle">
        Tire 5 cartes et choisis en une au hasard à la fin !
      </p>
      <input
        v-model="name"
        :disabled="attempts >= maxAttempts || finalCardPicked"
        placeholder="Entre ton prénom"
        @keyup.enter="handleClick"
      />
      <button
        @click="handleClick"
        :disabled="attempts >= maxAttempts || finalCardPicked"
      >
        Tenter ma chance
      </button>

      <div class="compteurs">
        <span class="good-count">{{ goodCount }}</span>
        <span class="sep">|</span>
        <span class="bad-count">{{ badCount }}</span>
      </div>

      <!-- Carte flip (essais classiques) -->
      <div
        v-if="result && attempts < maxAttempts"
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

      <!-- Cartes finales -->
      <div
        v-if="attempts >= maxAttempts && !finalCardPicked"
        class="final-cards-row"
      >
        <p class="final-choice-text">Choisis ta carte finale :</p>
        <div class="final-cards">
          <div
            v-for="(card, i) in 5"
            :key="'final-' + i"
            class="final-card-container"
            @click="pickFinalCard(i)"
            :class="{
              picked: pickedCardIndex === i,
              disabled: pickedCardIndex !== null && pickedCardIndex !== i,
            }"
          >
            <div
              class="final-card-flip-inner"
              :class="{ flipped: pickedCardIndex === i }"
            >
              <div class="final-card-front final-card">??</div>
              <div class="final-card-back final-card">
                {{ finalMessages[i] }}
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Résultat final -->
      <div v-if="finalCardPicked" class="final-result-block">
        <p class="final-choice-text">Voici ta carte finale !</p>
        <div class="final-big-card" :class="getFinalType(pickedCardIndex)">
          {{ finalMessages[pickedCardIndex] }}
        </div>
      </div>
    </div>
    <div class="separator"></div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import "./GoodOrBad.css";
import messages from "../assets/messages.json";

const maxAttempts = 6;
const name = ref("");
const result = ref("");
const isGood = ref(false);
const isBad = ref(false);
const flipping = ref(false);
const flipKey = ref(0);

const goodCount = ref(0);
const badCount = ref(0);
const attempts = ref(0);

const finalCardPicked = ref(false);
const pickedCardIndex = ref(null as null | number);
const finalMessages = ref<string[]>([]);

const handleClick = () => {
  if (!name.value.trim()) {
    alert("Entre ton prénom d'abord patate !");
    isGood.value = false;
    isBad.value = false;
    flipping.value = false;
    flipKey.value++;
    return;
  }
  if (attempts.value >= maxAttempts) return;

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
  attempts.value++;

  if (attempts.value === maxAttempts) {
    const pool = [...messages.good, ...messages.bad];
    finalMessages.value = [];
    for (let i = 0; i < 5; i++) {
      finalMessages.value.push(pool[Math.floor(Math.random() * pool.length)]);
    }
  }
};

function pickFinalCard(index: number) {
  if (pickedCardIndex.value !== null) return;
  pickedCardIndex.value = index;
  setTimeout(() => {
    finalCardPicked.value = true;
  }, 1200);
}

// Retourne "good" ou "bad" pour la carte finale
function getFinalType(index: number | null) {
  if (index === null) return "";
  const msg = finalMessages.value[index];
  if (messages.good.includes(msg)) return "good";
  if (messages.bad.includes(msg)) return "bad";
  return "";
}
</script>
