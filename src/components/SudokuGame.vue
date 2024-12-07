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
            const numbersToFill = difficulty === "easy" ? 38 : difficulty === "medium" ? 29 : 20;

            // Réinitialiser la grille
            const newGrid = Array.from({ length: 9 }, () => Array(9).fill(null)); // Utiliser null au lieu de 0

            // Fonction pour vérifier si un nombre est valide dans une position donnée
            const isValid = (grid: number[][], row: number, col: number, num: number): boolean => {
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

            // Remplir la grille avec des nombres aléatoires en respectant les règles
            let numbersFilled = 0;
            while (numbersFilled < numbersToFill) {
                let row = Math.floor(Math.random() * 9);
                let col = Math.floor(Math.random() * 9);
                let num = Math.floor(Math.random() * 9) + 1;

                // Vérifier si le nombre peut être placé
                if (newGrid[row][col] === null && isValid(newGrid, row, col, num)) {
                    newGrid[row][col] = num;
                    numbersFilled++;
                }
            }

            // Passer la grille générée au composant
            grid.value = newGrid;
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
