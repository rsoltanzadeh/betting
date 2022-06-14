<script setup>
import { ref, computed } from 'vue'

const options = ref([]);
let readyFlag = ref(false);

const description = computed(() => {
  readyFlag.value = checkReady();
  if (readyFlag.value) {
    calculateResult();
    return generateResultString();
  } else {
    for (const option of options.value) {
      option.result = undefined;
    }
    return "Enter a value for every option in order to get results.";
  }
});

function checkReady() {
  if (options.value.length < 2)
    return false;
  for(const option of options.value) {
    if(typeof option.odds !== 'number')
      return false;
  }
  return true;
}

function generateResultString() {
  const payout = options.value[0].odds * options.value[0].result;
  let resultString = ""; 1
  if (payout > 100) {
    resultString += `Jackpot! ${(payout-100).toFixed(2)}% profit guaranteed! Place your bets as instructed below.`;
  } else {
    resultString += `Unfortunately no guaranteed profit. A loss of ${(100-payout).toFixed(2)}% is guaranteed with the instructions below.`;
  }
  return resultString;
}

function calculateResult() {
  let totalCost = 0;
  for (let i = 0; i < options.value.length; i++) {
    let cost = 0;
    for (let j = 0; j < options.value.length; j++) {
      if (i == j)
        continue;
      cost += options.value[j].odds;
    }
    totalCost += cost;
    options.value[i].result = cost;
  }

  // normalize cost
  for (let option of options.value) {
    option.result *= (100 / totalCost);
    option.result = option.result.toFixed(2);
  }
}

function addOption() {
  options.value.push({
    "odds": 1
  });
}

function removeOption() {
  options.value.length--;
}

</script>

<template>
  <p>{{ description }}</p>
  <div class="buttons">
    <button @click="addOption">Add Option</button>
    <button @click="removeOption">Remove Option</button>
  </div>

  <div class="wrapper">
    <div class="options">
      <ul>
        <li class="option" v-for="(option, index) in options" :key="index">
          <div>
            <span v-show="readyFlag" class="result">{{ option.result }}%</span>
            <input v-model="option.odds" type="number" min="1" step="0.01" />
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
div.wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
}

div.options {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

span.result {
  margin-right: 2em;
}

div.buttons {
  margin-top: 2em;
}

button {
  padding: 0.5em;
}

ul {
  margin-top: 1em;
  list-style-type: none;
}

p {
  margin: 20px;
}
</style>
