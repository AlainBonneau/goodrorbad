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
        v-if="
          result && attempts <= maxAttempts && attempts > 0 && !finalCardPicked
        "
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

      <!-- Cartes finales (mélangées) -->
      <div
        v-if="attempts === maxAttempts && !finalCardPicked"
        class="final-cards-row"
      >
        <p class="final-choice-text">Choisis ta carte finale :</p>
        <div class="final-cards">
          <div
            v-for="(card, i) in shuffledCards"
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
                {{ card }}
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Résultat final -->
      <div v-if="finalCardPicked" class="final-result-block">
        <p class="final-choice-text">Voici ta carte finale !</p>
        <div class="final-big-card" :class="getFinalType(pickedCardIndex)">
          {{ name }}, {{ shuffledCards[pickedCardIndex] }}
        </div>
      </div>
    </div>
    <div class="separator"></div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";
import "./GoodOrBad.css";
import messages from "../assets/messages.json";

const maxAttempts = 5;
const name = ref("");
const result = ref("");
const isGood = ref(false);
const isBad = ref(false);
const flipping = ref(false);
const flipKey = ref(0);

const goodCount = ref(0);
const badCount = ref(0);
const attempts = ref(0);

const drawnCards = ref<string[]>([]);
const finalCardPicked = ref(false);
const pickedCardIndex = ref(null as null | number);
const shuffledCards = ref<string[]>([]);

function shuffle<T>(array: T[]): T[] {
  const arr = array.slice();
  for (let i = arr.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
  return arr;
}

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
  const pickedMessage = arr[Math.floor(Math.random() * arr.length)];
  result.value = `${name.value.trim()}, ${pickedMessage}`;

  flipping.value = false;
  flipKey.value++;
  setTimeout(() => {
    flipping.value = true;
  }, 20);

  if (good) goodCount.value++;
  else badCount.value++;
  attempts.value++;

  drawnCards.value.push(pickedMessage);

  if (attempts.value === maxAttempts) {
    shuffledCards.value = shuffle(drawnCards.value);
  }
};

function pickFinalCard(index: number) {
  if (pickedCardIndex.value !== null) return;
  pickedCardIndex.value = index;
  setTimeout(() => {
    finalCardPicked.value = true;
  }, 1200);
}

function getFinalType(index: number | null) {
  if (index === null) return "";
  const msg = shuffledCards.value[index];
  if (!msg) return "";
  if (messages.good.some((g) => msg.includes(g))) return "good";
  if (messages.bad.some((b) => msg.includes(b))) return "bad";
  return "";
}
</script>
