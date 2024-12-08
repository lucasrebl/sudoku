<template>
    <div class="grid-container">
        <div v-for="(row, rowIndex) in grid" :key="rowIndex" class="row">
            <div v-for="(cell, colIndex) in row" :key="colIndex" class="cell" @click="selectCell(rowIndex, colIndex)">
                <input type="text" maxlength="1" v-model="grid[rowIndex][colIndex]" class="cell-input"
                    :disabled="cell !== null" :class="{ 'invalid': isInvalid(rowIndex, colIndex) }"
                    :placeholder="cell === null ? '' : undefined" />
                <button v-if="isInvalid(rowIndex, colIndex) && grid[rowIndex][colIndex] !== null" class="reset-button"
                    @click.stop="resetCell(rowIndex, colIndex)">
                    X
                </button>
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
            required: true
        },
        invalidCells: {
            type: Array as PropType<{ row: number; col: number }[]>,
            required: true
        }
    },
    setup(props, { emit }) {
        // Vérifier si la cellule est invalide
        const isInvalid = (row: number, col: number) => {
            return props.invalidCells.some(cell => cell.row === row && cell.col === col);
        };

        // Sélectionner une cellule (émettre un événement)
        const selectCell = (row: number, col: number) => {
            emit("cell-selected", row, col);
        };

        // Réinitialiser la cellule (mettre null dans la grille)
        const resetCell = (row: number, col: number) => {
            if (props.grid[row][col] !== null) {
                props.grid[row][col] = null;
                // Vous pouvez également mettre à jour invalidCells ici si nécessaire
            }
        };

        return {
            isInvalid,
            selectCell,
            resetCell
        };
    }
});
</script>

<style scoped>
.grid-container {
    display: grid;
    grid-template-rows: repeat(9, 1fr);
    grid-template-columns: repeat(9, 1fr);
    gap: 5px;
    width: 360px;
}

.row {
    display: contents;
}

.cell {
    position: relative;
    width: 40px;
    height: 40px;
    display: flex;
    justify-content: center;
    align-items: center;
}

.cell-input {
    width: 100%;
    height: 100%;
    text-align: center;
    font-size: 18px;
    border: 1px solid #ccc;
    border-radius: 5px;
    transition: background-color 0.3s ease;
    background-color: white;
}

.cell-input:focus {
    background-color: #e0e0e0;
}

.invalid {
    color: red;
}

.reset-button {
    position: absolute;
    top: 0;
    right: 0;
    background: transparent;
    border: none;
    color: red;
    font-weight: bold;
    font-size: 12px;
    cursor: pointer;
}
</style>