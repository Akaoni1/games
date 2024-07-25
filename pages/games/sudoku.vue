<template>
    <aside>
        opciones
    </aside>
    <main>
        <header>
            <h1>Sudoku</h1>
            <Button :on-click="startGame" variant="primary">Start game</Button>
        </header>
        <div class="board">
            <section class="subgrid" v-for="(subgrid, subgridIndex) in board" :key="subgridIndex">
                <div class="cell" v-for="(cell, cellIndex) in subgrid" :key="cellIndex">
                    <input  v-model="subgrid[subgridIndex][cellIndex]" class="cell-input" maxlength="1">
                </div>
            </section>
        </div>
    </main>
</template>
<script setup lang="ts">
const board = ref<number[][]>([]);
const startGame = () => {
    const newBoard = Array.from({ length: 9 }, () => Array.from({ length: 9 }, () => 0));
    board.value = newBoard;
}
onMounted(() => {
    startGame();
});
</script>
<style scoped>
main {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 20px;
    padding: 20px;
    max-width: 1000px;
    margin: 0 auto;
}
.board {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);
    width: 100%;
    max-width: 500px;
    border: 1px solid var(--text-color);
    
}
.subgrid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);
    border: 1px solid var(--text-color);
}
.cell {
    display: flex;
    justify-content: center;
    align-items: center;
    border: 1px solid var(--text-color);
    aspect-ratio: 1/1;

}
.cell-input {
    width: 100%;
    height: 100%;
    font-size: 1.5rem;
    text-align: center;
    border: none;
    background-color: transparent;
    color: var(--text-color);
}


</style>