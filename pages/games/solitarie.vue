<template>
    <div>
        <h1>solitarie</h1>
        <button @click="startGame">Start game</button>
        <div class="board">
            <div class="deck" @click="showDeckCard">
                    <GamesSolitarieCard :card="card" v-for="card in deck"/>
            </div>
            <div class="discard">
                <GamesSolitarieCard :card="card" v-for="card in discard"/>
            </div>
            <div class="stacks">
                <div class="stack" v-for="stack in stacks">
                    <GamesSolitarieCard :card="card"v-for="card in stack" />
                </div>
            </div>
            <div class="piles">
                <div class="pile" v-for="pile in piles" >
                    <GamesSolitarieCard :card="card" v-for="(card,index) in pile" :style="`top: ${index*20}px`" />
                </div>
            </div>
        </div>
    </div>
</template>
<script setup lang="ts">
import type { Card } from '~/types/game/solitarie';
enum colors {
    H = "red",
    D = "red",
    S = "black",
    C = "black",
};

const deck = ref<Card[]>([]);
const piles = ref<Card[][]>([]);
const stacks = ref<Card[][]>([]);
const discard = ref<Card[]>([]);
//methods
const startGame = () => {
    resetBoard();
    shuffleDeck();
    fillPiles();
    showDeckCard();
};
const resetBoard = () => {
    deck.value = createDeck();
    piles.value = [[], [], [], [], [], [], []];
    stacks.value = [[], [], [], []];
    discard.value = [];
};
const createDeck = () => {
    const deck: Card[] = [];
    for (const type in colors) {
        for (let i = 1; i <= 13; i++) {
            deck.push({
                number: i,
                type: type,
                color: colors[type],
                isFaceUp: false,
            });
        }
    }
    return deck;
};
const shuffleDeck = () => {
    deck.value = deck.value.sort(() => Math.random() - 0.5);
};
const fillPiles = () => {
    for (let i = 0; i < 7; i++) {
        for (let j = 0; j < i + 1; j++) {
            const card = deck.value.pop();
            if (!card) {
                return;
            }
            if (j === i) {
                card.isFaceUp = true;
            }
            piles.value[i].push(card);
        }
    }
};
const showDeckCard = () => {
    if (deck.value.length === 0) {
        deck.value = discard.value.reverse();
        for (const card of deck.value) {
            card.isFaceUp = false;
        }
        discard.value = [];
        return;
    }
    const card = deck.value.pop();
    if (card) {
        card.isFaceUp = true;
        discard.value.push(card);
    }
};


</script>
<style scoped>
* {
    box-sizing: border-box;
}

.board {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 20px 10px;
    user-select: none;
    background-color: lime;
}

.deck {
    grid-column: 1 / 2;
    position: relative;
    aspect-ratio: 5/7;
    background-color: lightgray;
}

.discard {
    grid-column: 2 / 3;
    position: relative;
    aspect-ratio: 5/7;
    background-color: lightcoral;
}

.stacks {
    grid-column: 4 / 8;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    

    .stack {
        position: relative;
        aspect-ratio: 5/7;
        background-color: lightgreen;
    }
}

.piles {
    grid-column: 1 / 8;
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
    background-color: lightblue;

    .pile {
        aspect-ratio: 5/7;
        position: relative;
        

    }
}
</style>