<template>   
        <img v-if="props.card.isFaceUp" :src="`/images/cards/${props.card.number}${props.card.type}.svg`" />
        <img v-else src="/images/cards/black-back.svg" />
</template>
<script setup lang="ts">
import type { Card } from "~/types/game/solitarie";
const props = defineProps<{
    card: Card
}
>();
</script>
<style scoped>
img {
        max-width: 100%;
        max-height: 100%;
    }
</style>