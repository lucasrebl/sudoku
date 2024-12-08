<template>
    <div class="grid-container">
      <div v-for="(row, rowIndex) in grid" :key="rowIndex" class="row">
        <div
          v-for="(cell, colIndex) in row"
          :key="colIndex"
          class="cell"
          @click="selectCell(rowIndex, colIndex)"
        >
          <input
            type="text"
            maxlength="1"
            v-model="grid[rowIndex][colIndex]"
            class="cell-input"
            :disabled="cell !== null"
            :class="{ 'invalid': isInvalid(rowIndex, colIndex) }"
            :placeholder="cell === null ? '' : undefined"
          />
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
        type: Array as PropType<{ row: number; col: number }[]> ,
        required: true
      }
    },
    setup(props, { emit }) {
      // Méthode pour vérifier si une cellule est invalide
      const isInvalid = (row: number, col: number) => {
        return props.invalidCells.some(cell => cell.row === row && cell.col === col);
      };
  
      // Méthode pour sélectionner une cellule (génère l'événement avec 'emit')
      const selectCell = (row: number, col: number) => {
        emit("cell-selected", row, col); // Utilisation de 'emit' dans 'setup'
      };
  
      return {
        isInvalid,
        selectCell
      };
    }
  });
  </script>
  
  <style scoped>
  .grid-container {
    display: grid;
    grid-template-rows: repeat(9, 1fr);
    grid-template-columns: repeat(9, 1fr);
    gap: 0; /* Pas d'écart global entre les cellules */
    width: 360px; /* Définir une largeur fixe pour la grille */
    margin: 20px; /* Espacement autour de la grille */
  }
  
  .row {
    display: contents; /* Pour que chaque ligne utilise l'espace de la grille correctement */
  }
  
  .cell {
    width: 40px;
    height: 40px;
    display: flex;
    justify-content: center;
    align-items: center;
    border: 1px solid #ccc; /* Bordure par défaut pour les cellules */
  }
  
  /* Séparation des blocs 3x3 avec des bordures plus épaisses */
  .cell:nth-child(3n) {
    border-right: 2px solid #000; /* Bordure plus épaisse à la fin de chaque carré de 3 */
  }
  
  .row:nth-child(3n) .cell {
    border-bottom: 2px solid #000; /* Bordure plus épaisse à la fin de chaque carré de 3 */
  }
  
  /* Marges supplémentaires entre les blocs de 3x3 */
  .row:nth-child(3n) {
    margin-bottom: 5px; /* Marge entre les blocs 3x3 */
  }
  
  .cell:nth-child(3n+1) {
    margin-left: 5px; /* Marge à gauche des premiers éléments de chaque bloc */
  }
  
  .cell-input {
    width: 100%;
    height: 100%;
    text-align: center;
    font-size: 18px;
    border: none;
    outline: none;
    font-weight: bold;
    background-color: white;
  }
  
  .cell-input:focus {
    background-color: #e0e0e0;
  }
  
  .invalid {
    color: red;
  }
  </style>
  