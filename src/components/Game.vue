<script setup>
import '../assets/game.css'
import {ref} from 'vue'
import '../../public/sounds/1.mp3'

import('../../public/sounds/1.mp3').then(module => {
  blocks.value[0].sound = new Audio(module.default);
});
import('../../public/sounds/2.mp3').then(module => {
  blocks.value[1].sound = new Audio(module.default);
});
import('../../public/sounds/3.mp3').then(module => {
  blocks.value[2].sound = new Audio(module.default);
});
import('../../public/sounds/4.mp3').then(module => {
  blocks.value[3].sound = new Audio(module.default);
});

const round = ref(1)
const difficult = ref(1500)
const gameOver = ref(false)
const canTouch = ref(false)
const seq = ref([0, 0, 0])
const inputSeq = ref([])
const blocks = ref([
  {color: 'game__block_blue', active: false},
  {color: 'game__block_red', active: false},
  {color: 'game__block_yellow', active: false},
  {color: 'game__block_green', active: false}])

function randNumber() {
  return Math.trunc(Math.random() * (3 + 1))
}

function startGame() {
  gameOver.value = false
  round.value = 1
  canTouch.value = false
  inputSeq.value.splice(0, inputSeq.value.length);
  seq.value.splice(0, seq.value.length);
  seq.value.push(randNumber())
  showSeq(seq.value);
}

async function showSeq(seq) {
  canTouch.value = false
  for (const el of seq) {
    blocks.value[el].active = true;
    blocks.value[el].sound.play()
    await new Promise(resolve => {
      setTimeout(() => {
        blocks.value[el].active = false;
        resolve();
      }, difficult.value);
    });
    await new Promise(resolve => {
      setTimeout(() => {
        resolve();
      }, 100);
    });
  }
  canTouch.value = true
}


async function handleBlockClick(index, seq, inputSeq) {
  if (canTouch.value) {
    blocks.value[index].active = true;
    blocks.value[index].sound.play()
    await new Promise(resolve => {
      setTimeout(() => {
        blocks.value[index].active = false;
        resolve()
      }, 200)
    })
    inputSeq.push(index)
    if (compareArray(seq, inputSeq)) {
      if (seq.length === inputSeq.length) {
        round.value++
        seq.push(randNumber())
        inputSeq.splice(0, inputSeq.length);
        await new Promise(resolve => {
          setTimeout(() => {
            resolve();
          }, 500);
        });
        await showSeq(seq)
      }
    } else {
      endGame(true)
    }
  }
}

function endGame(tagGameOver) {
  //мб только одну строчку оставить
  gameOver.value = tagGameOver
  round.value = 1
  canTouch.value = false
  seq.value.splice(0, seq.value.length);
  inputSeq.value.splice(0, inputSeq.value.length);
}

function compareArray(seq, inputSeq) {
  let correct = true
  seq.forEach((el, index) => {
    if (el !== inputSeq[index]) {
      if (inputSeq[index] !== undefined) {
        correct = false
      }
    }
  })
  return correct
}

function selectDifficult(ms) {
  endGame(false)
  difficult.value = ms
}
</script>

<template>
  <div class="container main main_margin">
    <h2 class="main-header">Simon The Game</h2>
    <div class="game">
      <div class="game__blocks">
        <div v-for="(block, index) in blocks" :key="index"
             :class="['game__block', block.color, { 'active': block.active }]"
             @click="handleBlockClick(index,seq,inputSeq)"></div>
      </div>
      <div class="panel">
        <div class="round">Round {{ round }}</div>
        <div @click="startGame" class="button-start">Start</div>
        <div class="game-over">{{ gameOver ? "Вы проиграли!" : "" }}</div>
        <div class="difficult">
          <div class="difficult__head">Уровень сложности:</div>
          <ul class="difficult__body">
            <li class="difficult__elem">
              <input type="radio" name="difficult" id="dif1" checked>
              <label @click="selectDifficult(1500)" for="dif1">Легкий</label>
            </li>
            <li class="difficult__elem">
              <input type="radio" name="difficult" id="dif2">
              <label @click="selectDifficult(1000)" for="dif2">Средний</label>
            </li>
            <li class="difficult__elem">
              <input type="radio" name="difficult" id="dif3">
              <label @click="selectDifficult(400)" for="dif3">Тяжелый</label>
            </li>
          </ul>

        </div>
      </div>
    </div>
  </div>
</template>


<style scoped>

</style>