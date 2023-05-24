<script setup lang="ts">
import { onMounted, ref } from 'vue'
import Card from './components/Card.vue'

const cardList = ref<{ value: number; visible: boolean; position: number }[]>([])

onMounted(() => {
  createCardList()
})

function createCardList() {
  for (let i = 0; i < 16; i++) {
    cardList.value.push({
      value: i,
      visible: false,
      position: i
    })
  }
}

function flipCard(payload: {position: number}) {
  cardList.value[payload.position].visible = !cardList.value[payload.position].visible;
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
        @select-card="flipCard"
      />
    </section>
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
