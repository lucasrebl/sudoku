<template>
    <div class="difficulty-selector">
        <button 
            @click="selectDifficulty('easy')" 
            :class="{'selected': selectedDifficulty === 'easy'}"
        >
            Easy
        </button>
        <button 
            @click="selectDifficulty('medium')" 
            :class="{'selected': selectedDifficulty === 'medium'}"
        >
            Medium
        </button>
        <button 
            @click="selectDifficulty('hard')" 
            :class="{'selected': selectedDifficulty === 'hard'}"
        >
            Hard
        </button>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";

export default defineComponent({
    name: "DifficultySelector",
    emits: ["difficulty-selected"],
    setup(_, { emit }) {
        // Définir la difficulté sélectionnée par défaut
        const selectedDifficulty = ref('easy');  // Difficulté par défaut : easy

        const selectDifficulty = (difficulty: string) => {
            selectedDifficulty.value = difficulty;
            emit("difficulty-selected", difficulty); // Émettre l'événement
        };

        return {
            selectDifficulty,
            selectedDifficulty,
        };
    },
});
</script>

<style scoped>
.difficulty-selector {
    margin-bottom: 20px;
    display: flex;
    justify-content: center;
    gap: 10px;
}

button {
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
    border: 1px solid #ccc;
    border-radius: 5px;
    background-color: #f0f0f0;
    transition: background-color 0.3s ease;
}

/* Ajouter une couleur différente pour chaque difficulté */
button.selected {
    background-color: #007BFF; /* Bleu pour la difficulté sélectionnée */
    color: white;
}

/* Style des boutons non sélectionnés */
button:not(.selected):hover {
    background-color: #e0e0e0;
}
</style>
