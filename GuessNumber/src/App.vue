<script lang="ts" setup>
import { ref, computed, watch } from "vue";

//tunable parameters
const numberOfIteratiuons = 8;
const numberOfCells = 4;
const containDublicate = false;
const numbers = [
  "0",
  "1",
  "2",
  "3",
  "4",
  "5",
  "6",
  "7",
  "8",
  "9",
];

//essential parameters
let beliefs: string[] = [];

let tem = [""];

for (let i = 0; i < numberOfCells; i++) {
  let length = tem.length;
  for (let k = 0; k < length; k++) {
    for (let j = 0; j < numbers.length; j++) {
      if (containDublicate || !tem[k].includes(numbers[j])) {
        let v = tem[k] + numbers[j];
        tem.push(v);
      }
    }
  }
}
for (let i = 0; i < tem.length; i++) {
  if (tem[i].length == numberOfCells) {
    beliefs.push(tem[i]);
  }
}

let numberOfNumbers = numbers.length;

let number = "";
let indices: number[] = [];
for (let i = 0; i < numberOfCells; i++) {
  let index = Math.floor(Math.random() * numberOfNumbers);
  if (!containDublicate) {
    while (indices.includes(index)) {
      index = Math.floor(Math.random() * numberOfNumbers);
    }
  }
  indices.push(index);
  number = number + numbers[index];
}
console.log(number);

let temp = [];
let temp2 = [];
for (let i = 0; i < numberOfIteratiuons; i++) {
  let tem = [];
  for (let j = 0; j < numberOfCells; j++) {
    tem.push("");
  }
  temp.push(tem);
  temp2.push(["", ""]);
}

const allCells = ref(temp);
const results = ref(temp2);
const currntRow = ref(0);
const currntCell = ref(0);
const testNumber = ref();
const correct = ref(false);
const buttonClicked = ref<string>("");
const type = computed(() => {
  if (numbers.includes(buttonClicked.value)) {
    return "number";
  }
  if (buttonClicked.value == "Enter") {
    return "Enter";
  }
  if (buttonClicked.value == "Backspace") {
    return "Backspace";
  }
  if (buttonClicked.value == "ArrowRight") {
    return "ArrowRight";
  }
  if (buttonClicked.value == "ArrowLeft") {
    return "ArrowLeft";
  }
  return "none";
});
function repeat() {
  3;
}

window.addEventListener("keyup", (e) => {
  buttonClicked.value = e.key;
  if (type.value == "number") {
    allCells.value[currntRow.value][currntCell.value] = buttonClicked.value;
    if (currntCell.value < numberOfCells - 1) {
      currntCell.value += 1;
    }
  } else if (type.value == "ArrowLeft" && currntCell.value > 0) {
    currntCell.value -= 1;
  } else if (
    type.value == "ArrowRight" &&
    currntCell.value < numberOfCells - 1
  ) {
    currntCell.value += 1;
  } else if (type.value == "Backspace") {
    allCells.value[currntRow.value][currntCell.value] = "";
  } else if (
    type.value == "Enter" &&
    !allCells.value[currntRow.value].includes("") &&
    currntRow.value < numberOfIteratiuons
  ) {
    testNumber.value = allCells.value[currntRow.value];
  }
});

watch(testNumber, (testnum) => {
  let r = getRelust(number, testnum);
  if (Number(r[1]) == numberOfCells) {
    correct.value = true;
  }

  if (correct.value) {
    repeat();
  }
  results.value[currntRow.value] = r;
  updatebeleifs(testnum, r);

  console.log(beliefs, beliefs[Math.floor(Math.random() * beliefs.length)]);

  currntRow.value += 1;
  currntCell.value = 0;
});

function getRelust(CorrectNumber: string, guess: string[]) {
  let num = [...guess];
  let correctNumberPosition = [0, 0];
  let positionsCorrect = [];

  for (let i = 0; i < numberOfCells; i++) {
    if (num[i] == CorrectNumber[i]) {
      correctNumberPosition[0] += 1;
      correctNumberPosition[1] += 1;
      num[i] = "-1";
      positionsCorrect.push(i);
    } else {
      correct.value = false;
    }
  }
  for (let i = 0; i < numberOfCells; i++) {
    if (!positionsCorrect.includes(i) && num.includes(CorrectNumber[i])) {
      num[num.indexOf(CorrectNumber[i])] = "-1";
      correctNumberPosition[0] += 1;
    }
  }
  return [
    correctNumberPosition[0].toString(),
    correctNumberPosition[1].toString(),
  ];
}

function updatebeleifs(num: string, result: string[]) {
  let tem: string[] = [];
  let arr: string[] = [];
  for (let c of num) {
    arr.push(c);
  }
  for (let e of beliefs) {
    let r = getRelust(e, arr);
    if (r[0] == result[0] && r[1] == result[1]) {
      tem.push(e);
    }
  }
  beliefs = tem;
}
</script>

<template>
  <main>
    <div class="container">
      <div
        v-for="iteration in numberOfIteratiuons"
        class="guess"
        :key="iteration"
      >
        <div
          v-for="cell in numberOfCells"
          class="cell"
          :key="cell"
          :class="{
            selected: currntCell == cell - 1 && currntRow == iteration - 1,
          }"
        >
          {{ allCells[iteration - 1][cell - 1] }}
        </div>
        <div class="result">
          <span>{{ results[iteration - 1][0] }} </span>right number
          <span>{{ results[iteration - 1][1] }} </span>right positions
        </div>
      </div>
    </div>
    <div class="correct" v-if="correct">Correct</div>
    <div class="correct" v-if="currntRow == numberOfIteratiuons">lose</div>
  </main>
</template>

<style>
html {
  background-color: #2c2c2c;
}
.container {
  display: flex;
  flex-direction: column;
  margin: auto;
  margin-top: 32px;
  gap: 12px;
}

.guess {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  gap: 8px;
}

.cell {
  width: 4rem;
  aspect-ratio: 1;
  background-color: #3c3c3c;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 3rem;
}
.selected {
  scale: 1.1;
  background-color: #6c6c6c;
}
.result {
  font-size: 1.5rem;
  text-transform: capitalize;
  display: flex;
  flex-direction: row;
  gap: 1rem;
  margin-left: 4rem;
  color: #ddd;
  width: 24rem;
}
.result > *:first-of-type {
  color: green;
}
.result > *:last-of-type {
  color: yellow;
}

.correct {
  text-transform: capitalize;
  width: fit-content;
  font-size: 4rem;
  margin: auto;
  margin-top: 2rem;
  color: red;
}
</style>
