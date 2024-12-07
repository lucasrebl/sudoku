<template>
    <div class="sudoku-grid">
        <div v-for="(row, rowIndex) in grid" :key="rowIndex" class="row">
            <div v-for="(cell, colIndex) in row" :key="colIndex" class="cell">
                <input 
                    type="text" 
                    maxlength="1" 
                    v-model="grid[rowIndex][colIndex]" 
                    class="cell-input"
                    :disabled="cell !== null"
                    :placeholder="cell === null ? '' : undefined" />
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import type { PropType } from "vue";

export default defineComponent({
    name: "SudokuGrid",
    props: {
        grid: {
            type: Array as PropType<(number | null)[][]>,
            required: true,
        },
    },
});
</script>

<style scoped>
.sudoku-grid {
    display: grid;
    grid-template-columns: repeat(9, 1fr);
    gap: 2px;
    max-width: 360px;
}

.row {
    display: contents;
}

.cell {
    width: 40px;
    height: 40px;
    border: 1px solid #ccc;
    display: flex;
    align-items: center;
    justify-content: center;
}

.cell:nth-child(3n) {
    border-right: 2px solid #000;
}

.row:nth-child(3n) .cell {
    border-bottom: 2px solid #000;
}

.cell:nth-child(3n+1) {
    border-left: 2px solid #000;
}

.row:nth-child(3n+1) .cell {
    border-top: 2px solid #000; 
}

.cell-input {
    width: 100%;
    height: 100%;
    text-align: center;
    border: none;
    outline: none;
    font-size: 18px;
    font-weight: bold;
    background-color: white;
}

.cell-input::placeholder {
    color: transparent;
}

</style>
