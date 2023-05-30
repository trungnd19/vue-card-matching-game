<script setup lang="ts">
import { onMounted, ref, watch, computed } from 'vue'
import Card from './components/Card.vue'
import _ from 'lodash'

const cardList = ref<{ value: number; visible: boolean; position: number, matched: boolean }[]>([])
const userSelection = ref<{position: number, faceValue: number}[]>([])
const status = computed(() => {
  if(remainingPairs.value === 0) {
    return "You win!"
  } else {
    return `Remaining Pairs: ${remainingPairs.value}`
  }
})
const remainingPairs = computed(() => {
  const remainingCards = cardList.value.filter(card => card.matched === false).length;
  return remainingCards / 2
})

onMounted(() => {
  // createCardList()
})

watch(userSelection, (currentValue) => {
  if(currentValue.length === 2) {
    const cardOne = currentValue[0];
    const cardTwo = currentValue[1];

    // if 2 cards matches
    if(cardOne.faceValue === cardTwo.faceValue) {
      // status.value = "Matched!"
      // Nếu matched => không cần flip lại
      cardList.value[cardOne.position].matched = true;
      cardList.value[cardTwo.position].matched = true;
    } else {
      // Bug: Allow user to see incorrect match before flipping
      setTimeout(() => {
        // status.value = "Mismatched!";
        cardList.value[cardOne.position].visible = false;
        cardList.value[cardTwo.position].visible = false
      }, 2000)
    }

    userSelection.value.length = 0
    // reset selected array
  }
}, {deep: true})

function createCardList() {
  for (let i = 0; i < 16; i++) {
    cardList.value.push({
      value: 8,
      visible: false,
      position: i,
      matched: false // nếu matched => không cần flip lại nữa (TODO: nếu matched => cho biến mất)
    })
  }
}

const cardItems = [1,2,3,4,5,6,7,8]
cardItems.forEach(item => {
  cardList.value.push({
    value: item,
    visible: true,
    position: null,
    matched: false
  })

  cardList.value.push({
    value: item,
    visible: true,
    position: null,
    matched: false
  })
})

cardList.value = cardList.value.map((card, index) => {
  return {
    ...card,
    position: index
  }
})

function flipCard(payload: {position: number, faceValue: number}) {
  cardList.value[payload.position].visible = true;

  if(userSelection.value[0]) {
    // Bug: user cannot register same card
    if(userSelection.value[0].position === payload.position && userSelection.value[0].faceValue === payload.faceValue) {
      return;
    } else {
      userSelection.value[1] = payload
    }
  } else {
    userSelection.value[0] = payload
  }
}

function shuffleCards() {
  cardList.value = _.shuffle(cardList.value)
}

function restartGame() {
  shuffleCards();
  cardList.value = cardList.value.map((card, index) => {
    return {
      ...card,
      matched: false,
      visible: false,
      position: index
    }
  })

}
</script>

<template>
  <div class="game-wrapper">
    <h1>Peek-a-Vue</h1>
    <section class="game-board">
      <Card
        v-for="(card, index) in cardList"
        :value="card.value"
        :visible="card.visible"
        :key="`card-${index}`"
        :position="card.position"
        :matched="card.matched"
        @select-card="flipCard"
      />
    </section>
    <h2>{{ userSelection }}</h2>
    <h2>{{ status }}</h2>
    <button @click="shuffleCards">Shuffle</button>
    <button @click="restartGame">Restart game</button>
  </div>
</template>

<style scoped>
.game-board {
  display: grid;
  grid-template-columns: 100px 100px 100px 100px;
  grid-template-rows: 100px 100px 100px 100px;
  grid-column-gap: 30px;
  grid-row-gap: 30px;
  justify-content: center;
}

.game-wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>
