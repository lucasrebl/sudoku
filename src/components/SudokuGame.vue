<template>
    <div class="sudoku-game">
        <DifficultySelector @difficulty-selected="generateGrid" />
        <div class="game-container">
            <SudokuGrid :grid="grid" @cell-selected="selectCell" />
            <div class="number-buttons">
                <button v-for="num in numbers" :key="num" @click="setNumber(num)" class="number-button">
                    {{ num }}
                </button>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import SudokuGrid from "./SudokuGrid.vue";
import DifficultySelector from "./DifficultySelector.vue";

export default defineComponent({
    name: "SudokuGame",
    components: { SudokuGrid, DifficultySelector },
    setup() {
        const grid = ref<(number | null)[][]>(Array.from({ length: 9 }, () => Array(9).fill(null)));
        const fullGrid = ref<(number | null)[][]>(Array.from({ length: 9 }, () => Array(9).fill(null))); // Grille complète avec tous les chiffres révélés
        const selectedRow = ref<number | null>(null);
        const selectedCol = ref<number | null>(null);
        const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];
        const currentNumber = ref<number | null>(null);

        // Fonction pour afficher la grille complète (avec les chiffres révélés)
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

        // Fonction pour générer une grille valide complète
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
            fullGrid.value = fullGridCopy; // Stocker la grille complète (avec tous les chiffres révélés)

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

            grid.value = playableGrid; // Met à jour la grille jouable (avec des cases vides)
            logGrid(); // Affiche les deux grilles dans la console
        };

        const selectCell = (row: number, col: number) => {
            selectedRow.value = row;
            selectedCol.value = col;
        };

        const setNumber = (num: number) => {
            if (selectedRow.value !== null && selectedCol.value !== null) {
                if (grid.value[selectedRow.value][selectedCol.value] === null) {
                    grid.value[selectedRow.value][selectedCol.value] = num;
                    logGrid(); // Affiche la grille dans la console après chaque modification
                }
            }
        };

        generateGrid('easy'); // Générer la grille au départ

        return {
            grid,
            generateGrid,
            selectCell,
            setNumber,
            numbers
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

.number-buttons {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    margin-left: 20px;
}

.number-button {
    font-size: 20px;
    padding: 10px;
    margin: 5px;
    cursor: pointer;
    background-color: #f0f0f0;
    border: 1px solid #ccc;
    border-radius: 5px;
    transition: background-color 0.3s ease;
}

.number-button:hover {
    background-color: #e0e0e0;
}
</style>
