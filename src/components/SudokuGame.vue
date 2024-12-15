<template>
    <div class="sudoku-game">
        <DifficultySelector @difficulty-selected="generateGrid" />
        <div class="game-container">
            <SudokuGrid :grid="grid" @cell-selected="selectCell" :invalid-cells="invalidCells" />
            <NumberButtons :numbers="numbers" :isButtonDisabled="isButtonDisabled" @number-selected="setNumber" />
        </div>
        <LifeCounter :remaining-lives="remainingLives" />
        <GameOverModal :isVisible="gameOver" @restart="restartGame" />
        <VictoryModal :isVisible="victory" @restart="restartGame" />
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, onBeforeUnmount } from "vue";
import SudokuGrid from "./SudokuGrid.vue";
import DifficultySelector from "./DifficultySelector.vue";
import NumberButtons from "./NumberButtons.vue";
import LifeCounter from "./LifeCounter.vue";
import GameOverModal from "./GameOverModal.vue";
import VictoryModal from "./VictoryModal.vue";

export default defineComponent({
    name: "SudokuGame",
    components: { SudokuGrid, DifficultySelector, NumberButtons, LifeCounter, GameOverModal, VictoryModal },
    setup() {
        const grid = ref<(number | null)[][]>(Array.from({ length: 9 }, () => Array(9).fill(null)));
        const fullGrid = ref<(number | null)[][]>(Array.from({ length: 9 }, () => Array(9).fill(null)));
        const selectedRow = ref<number | null>(null);
        const selectedCol = ref<number | null>(null);
        const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];
        const invalidCells = ref<{ row: number, col: number }[]>([]);
        const placedNumbers = ref<Record<number, number>>({
            1: 0,
            2: 0,
            3: 0,
            4: 0,
            5: 0,
            6: 0,
            7: 0,
            8: 0,
            9: 0
        });

        const maxLives = 3;
        const remainingLives = ref(maxLives);
        const gameOver = ref(false);
        const victory = ref(false);

        const logGrid = () => {
            console.log("Grille complète (révélée) :");
            fullGrid.value.forEach(row => {
                console.log(row.map(cell => (cell === null ? "." : cell)).join(" "));
            });
            console.log("\nGrille actuelle (jouable) :");
            grid.value.forEach(row => {
                console.log(row.map(cell => (cell === null ? "." : cell)).join(" "));
            });
        };

        const generateGrid = (difficulty: string) => {
            const generateFullGrid = (): (number | null)[][] => {
                const grid: (number | null)[][] = Array.from({ length: 9 }, () => Array(9).fill(null));

                const isValid = (grid: (number | null)[][], row: number, col: number, num: number): boolean => {
                    for (let i = 0; i < 9; i++) {
                        if (grid[row][i] === num) return false;
                    }
                    for (let i = 0; i < 9; i++) {
                        if (grid[i][col] === num) return false;
                    }
                    const startRow = Math.floor(row / 3) * 3;
                    const startCol = Math.floor(col / 3) * 3;
                    for (let i = startRow; i < startRow + 3; i++) {
                        for (let j = startCol; j < startCol + 3; j++) {
                            if (grid[i][j] === num) return false;
                        }
                    }
                    return true;
                };

                const fillGrid = (grid: (number | null)[][]): boolean => {
                    for (let row = 0; row < 9; row++) {
                        for (let col = 0; col < 9; col++) {
                            if (grid[row][col] === null) {
                                const numbers = Array.from({ length: 9 }, (_, i) => i + 1).sort(() => Math.random() - 0.5);
                                for (const num of numbers) {
                                    if (isValid(grid, row, col, num)) {
                                        grid[row][col] = num;
                                        if (fillGrid(grid)) return true;
                                        grid[row][col] = null;
                                    }
                                }
                                return false;
                            }
                        }
                    }
                    return true;
                };

                fillGrid(grid);
                return grid;
            };

            const fullGridCopy = generateFullGrid();
            fullGrid.value = fullGridCopy;
            const numbersToRemove = difficulty === "easy" ? 38 : difficulty === "medium" ? 49 : 60;
            const playableGrid: (number | null)[][] = fullGridCopy.map(row => [...row]);

            for (let i = 0; i < numbersToRemove; i++) {
                let row, col;
                do {
                    row = Math.floor(Math.random() * 9);
                    col = Math.floor(Math.random() * 9);
                } while (playableGrid[row][col] === null);

                playableGrid[row][col] = null;
            }

            grid.value = playableGrid;
            logGrid();
        };

        const selectCell = (row: number, col: number) => {
            selectedRow.value = row;
            selectedCol.value = col;
        };

        const validateNumber = (row: number, col: number, num: number) => {
            if (fullGrid.value[row][col] !== num) {
                if (!invalidCells.value.some(cell => cell.row === row && cell.col === col)) {
                    invalidCells.value.push({ row, col });
                    remainingLives.value--;
                    if (remainingLives.value <= 0) {
                        gameOver.value = true;
                    }
                }
            } else {
                invalidCells.value = invalidCells.value.filter(cell => cell.row !== row || cell.col !== col);
            }
        };

        const checkVictory = () => {
            for (let row = 0; row < 9; row++) {
                for (let col = 0; col < 9; col++) {
                    if (grid.value[row][col] !== fullGrid.value[row][col]) {
                        return false;
                    }
                }
            }
            return true;
        };

        const setNumber = (num: number) => {
            if (selectedRow.value !== null && selectedCol.value !== null) {
                if (grid.value[selectedRow.value][selectedCol.value] === null) {
                    grid.value[selectedRow.value][selectedCol.value] = num;
                    validateNumber(selectedRow.value, selectedCol.value, num);
                    checkNumberPlacement(num);
                    if (checkVictory()) {
                        victory.value = true;
                    }
                    logGrid();
                }
            }
        };


        const checkNumberPlacement = (num: number) => {
            let count = 0;
            for (let row = 0; row < 9; row++) {
                for (let col = 0; col < 9; col++) {
                    if (grid.value[row][col] === num && fullGrid.value[row][col] === num) {
                        count++;
                    }
                }
            }
            placedNumbers.value[num] = count;
        };

        const isButtonDisabled = (num: number) => {
            const totalCount = fullGrid.value.flat().filter(cell => cell === num).length;
            const placedCount = placedNumbers.value[num];
            return placedCount === totalCount;
        };

        const restartGame = () => {
            window.location.reload();
        };

        const handleKeydown = (event: KeyboardEvent) => {
            event.preventDefault();
        };

        onMounted(() => {
            window.addEventListener("keydown", handleKeydown);
        });

        onBeforeUnmount(() => {
            window.removeEventListener("keydown", handleKeydown);
        });

        generateGrid("easy");

        return {
            grid,
            generateGrid,
            selectCell,
            setNumber,
            numbers,
            invalidCells,
            remainingLives,
            gameOver,
            victory,
            restartGame,
            isButtonDisabled,
        };
    },
});
</script>

<style scoped>
.sudoku-game {
    display: flex;
    flex-direction: column;
    align-items: center;
}

.game-container {
    display: flex;
}
</style>
