<template>
    <div class="card" @click="selectCard">
        <div v-if="visible" class="card-face is-front">
            {{ value }}
        </div>
        <div v-else class="card-face is-back">
            Back
        </div>
    </div>
</template>

<script setup lang="ts">
// import { ref } from 'vue';

const props = defineProps<{
  value: number,
  visible: boolean,
  position: number
}>()

const emit = defineEmits<{
  (e: 'select-card', value: {position: number}): void
}>()

function selectCard() {
    emit('select-card', {
        position: props.position
    })
}

</script>

<style scoped>
.card {
  border: 5px solid #ccc;
}

.card-face {
    width: 100%;
    height: 100%;
}

.card-face.is-front {
    background-color: red;
    color: white
}

.card-face.is-back {
    background-color: blue;
    color: white
}
</style>