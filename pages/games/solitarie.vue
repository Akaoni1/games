<template>
    <main>
        <header>
            <h1>solitarie</h1>
            <Button :on-click="startGame" variant="primary">Start game</Button>
        </header>
        <div class="board">
            <section class="deck" @click="showDeckCard()">

                <button class="card" draggable="true" v-for="card in deck" :key="`${card.number}${card.type}`">
                    <GamesSolitarieCard :card="card" />
                </button>
            </section>
            <section class="discard">
                <button class="card" draggable="true" v-for="card in discard" :key="`${card.number}${card.type}`"
                    @dragstart="handleDragStart(card)">
                    <GamesSolitarieCard :card="card" />
                </button>
            </section>
            <section class="stacks">
                <div class="stack" v-for="(stack, index) in stacks" @drop="handleDrop(stack, `stack-${index}`)"
                    @dragover.prevent="">
                    <button class="card" draggable="true" v-for="card in stack" :key="`${card.number}${card.type}`"
                        @dragstart="handleDragStart(card)">
                        <GamesSolitarieCard :card="card" />
                    </button>
                </div>
            </section>
            <section class="piles">
                <div class="pile" v-for="(pile, i) in piles" @drop="handleDrop(pile, `pile-${i}`)" @dragover.prevent="">
                    <button class="card" draggable="true" v-for="(card, index) in pile" :style="`top: ${index * 20}px`"
                        :key="`${card.number}${card.type}`" @dragstart="handleDragStart(card)"
                        @click="handleCardClick(card)">
                        <GamesSolitarieCard :card="card" />
                    </button>
                </div>
            </section>
        </div>
    </main>
</template>
<script setup lang="ts">
import type { Card } from '~/types/game/solitarie';
enum colors {
    "H" = "red",
    "D" = "red",
    "S" = "black",
    "C" = "black",
};

const deck = ref<Card[]>([]);
const piles = ref<Card[][]>([]);
const stacks = ref<Card[][]>([]);
const discard = ref<Card[]>([]);
const selectedCard = ref<Card | null>(null);
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
                zone: 'deck',
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
            card.zone = `pile-${i}`;
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
            card.zone = 'deck';
        }
        discard.value = [];
        return;
    }
    const card = deck.value.pop();
    if (card) {
        card.isFaceUp = true;
        card.zone = 'discard';
        discard.value.push(card);
    }
};
//drag and drop
const handleDragStart = (card: Card) => {
    selectedCard.value = card;
};
const addCardToStack = (zone: Card[], type: string) => {
    if (!selectedCard.value) {
        return;
    }
    const previousZone = selectedCard.value.zone;
    if (previousZone === 'discard') {
        const cardIndex = discard.value.findIndex(card => card === selectedCard.value);
        discard.value.splice(cardIndex, 1);
    }
    if (previousZone.startsWith('pile')) {
        const pileIndex = parseInt(previousZone.split('-')[1]);
        const cardIndex = piles.value[pileIndex].findIndex(card => card === selectedCard.value);
        piles.value[pileIndex].splice(cardIndex, 1);
    }
    if (previousZone.startsWith('stack')) {
        const stackIndex = parseInt(previousZone.split('-')[1]);
        const cardIndex = stacks.value[stackIndex].findIndex(card => card === selectedCard.value);
        stacks.value[stackIndex].splice(cardIndex, 1);
    }
    selectedCard.value.zone = type;
    zone.push(selectedCard.value);
    selectedCard.value = null;
    endGame();
};
const addCardToPile = (zone: Card[], type: string) => {
    if (!selectedCard.value) {
        return;
    }
    const previousZone = selectedCard.value.zone;
    if (previousZone === 'discard') {
        const cardIndex = discard.value.findIndex(card => card === selectedCard.value);
        discard.value.splice(cardIndex, 1);
    }
    if (previousZone.startsWith('stack')) {
        const stackIndex = parseInt(previousZone.split('-')[1]);
        const cardIndex = stacks.value[stackIndex].findIndex(card => card === selectedCard.value);
        stacks.value[stackIndex].splice(cardIndex, 1);
    }
    if (previousZone.startsWith('pile')) {
        const pileIndex = parseInt(previousZone.split('-')[1]);
        const cardIndex = piles.value[pileIndex].findIndex(card => card === selectedCard.value);
        //si no es la ultima carta se lleva todas las cartas de arriba
        if (cardIndex !== piles.value[pileIndex].length - 1) {
            const cards = piles.value[pileIndex].splice(cardIndex);
            for (const card of cards) {
                card.zone = type;
                zone.push(card);
            }
            selectedCard.value = null;
            return;
        }
        piles.value[pileIndex].splice(cardIndex, 1);
    }
    selectedCard.value.zone = type;
    zone.push(selectedCard.value);
    selectedCard.value = null;
};
const handleCardClick = (card: Card) => {
    // reviasa si es la ultima carta de la pila
    if (card.zone.startsWith('pile')) {
        const pileIndex = parseInt(card.zone.split('-')[1]);
        if (piles.value[pileIndex][piles.value[pileIndex].length - 1] !== card) {
            return;
        }
    }
    if (card.isFaceUp) {
        return;
    }
    card.isFaceUp = true;
};
const handleDrop = (zone: Card[], type: string) => {
    if (!selectedCard.value) {
        return;
    }
    if (type.startsWith('stack')) {
        if (zone.length === 0 && selectedCard.value.number === 1) {
            addCardToStack(zone, type);
            return;
        }
        if (zone.length === 0) {
            return;
        }
        const lastCard = zone[zone.length - 1];
        if (selectedCard.value.number === lastCard.number + 1 && selectedCard.value.type === lastCard.type) {
            addCardToStack(zone, type);
            return;
        }
    }
    if (type.startsWith('pile')) {
        if (zone.length === 0 && selectedCard.value.number === 13) {
            addCardToPile(zone, type);
            return;
        }
        if (zone.length === 0) {
            return;
        }
        const lastCard = zone[zone.length - 1];
        if (selectedCard.value.number === lastCard.number - 1 && selectedCard.value.color !== lastCard.color) {
            addCardToPile(zone, type);
            return;
        }
    }
    selectedCard.value = null;
};
const endGame = () => {
    // si todas los stack tienen 13 cartas
    if (stacks.value.every(stack => stack.length === 13)) {
        alert('You win');
    }
};
onMounted(() => {
    startGame();
});
</script>
<style scoped>
* {
    box-sizing: border-box;
}

main {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 20px;
    padding: 20px;
    max-width: 1000px;
    margin: 0 auto;
}

h1 {
    text-align: center;
    margin: 0;
}

.card {
    max-width: 100%;
    aspect-ratio: 5/7;
    cursor: pointer;
    position: absolute;
    border: 0;
    padding: 0;
    background-color: transparent;
}

.card:hover {
    transform: translateY(-10%);
}

.board {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 20px 10px;
    user-select: none;
    width: 100%;

}

.deck {
    grid-column: 1 / 2;
    position: relative;
    aspect-ratio: 5/7;
}

.discard {
    grid-column: 2 / 3;
    position: relative;
    aspect-ratio: 5/7;
}

.stacks {
    grid-column: 4 / 8;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    border: 1px solid var(--text-color);
    gap: 10px;

    .stack {
        position: relative;
        aspect-ratio: 5/7;
    }
}

.piles {
    grid-column: 1 / 8;
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;

    .pile {
        aspect-ratio: 5/7;
        position: relative;
    }
}
</style>