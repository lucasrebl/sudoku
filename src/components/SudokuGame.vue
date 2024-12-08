<template>
    <div class="sudoku-game">
        <DifficultySelector @difficulty-selected="generateGrid" />
        <SudokuGrid :grid="grid" />
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
        // Utilisation de null pour les cases vides
        const grid = ref<(number | null)[][]>(Array.from({ length: 9 }, () => Array(9).fill(null)));

        const generateGrid = (difficulty: string) => {
            // Fonction pour générer une grille complète valide
            const generateFullGrid = (): (number | null)[][] => {
                const grid: (number | null)[][] = Array.from({ length: 9 }, () => Array(9).fill(null));

                const isValid = (grid: (number | null)[][], row: number, col: number, num: number): boolean => {
                    // Vérifier la ligne
                    for (let i = 0; i < 9; i++) {
                        if (grid[row][i] === num) return false;
                    }

                    // Vérifier la colonne
                    for (let i = 0; i < 9; i++) {
                        if (grid[i][col] === num) return false;
                    }

                    // Vérifier le carré de 3x3
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

            // Générer une grille complète
            const fullGrid = generateFullGrid();

            // Supprimer des chiffres pour créer une grille jouable
            const numbersToRemove = difficulty === "easy" ? 38 : difficulty === "medium" ? 49 : 60;
            const playableGrid: (number | null)[][] = fullGrid.map(row => [...row]);

            for (let i = 0; i < numbersToRemove; i++) {
                let row, col;
                do {
                    row = Math.floor(Math.random() * 9);
                    col = Math.floor(Math.random() * 9);
                } while (playableGrid[row][col] === null);

                playableGrid[row][col] = null;
            }

            // Mettre à jour la grille
            grid.value = playableGrid;
        };


        generateGrid('easy');

        return {
            grid,
            generateGrid,
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
</style>
