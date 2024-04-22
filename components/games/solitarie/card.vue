<template>
    <button class="card" draggable="true">

        <img v-if="props.card.isFaceUp" :src="`/images/cards/${props.card.number}${props.card.type}.svg`" />
        <img v-else src="/images/cards/black-back.svg" />
    </button>
</template>
<script setup lang="ts">
import type { Card } from "~/types/game/solitarie";
const props = defineProps<{
    card: Card
}
>();
</script>
<style scoped>
.card {
    max-width: 100%;
    aspect-ratio: 5/7;
    cursor: pointer;
    position: absolute;
    border:0;
    padding: 0;
    background-color: transparent;

    img {
        max-width: 100%;
        max-height: 100%;
    }
}

.card:hover {
    transform: translateY(-10px);
}

.card:focus {
    border: 2px solid blue;
}
</style>