<template>
    <div class="number-buttons">
        <button v-for="num in numbers" :key="num" @click="onNumberClick(num)" :disabled="isButtonDisabled(num)"
            class="number-button">
            {{ num }}
        </button>
    </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import type { PropType } from "vue";

export default defineComponent({
    name: "NumberButtons",
    props: {
        numbers: {
            type: Array as PropType<number[]>,
            required: true,
        },
        isButtonDisabled: {
            type: Function as PropType<(num: number) => boolean>,
            required: true,
        },
    },
    emits: ["number-selected"],
    methods: {
        onNumberClick(num: number) {
            this.$emit("number-selected", num);
        },
    },
});
</script>

<style scoped>
.number-buttons {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    margin-left: 20px;
}

.number-button {
    font-size: 20px;
    padding: 20px;
    cursor: pointer;
    background-color: white;
    border: 1px solid #ccc;
    border-radius: 5px;
    transition: background-color 0.3s ease;
    text-align: center;
}

.number-button:hover {
    background-color: #e0e0e0;
}

.number-button:disabled {
    background-color: #e0e0e0;
    cursor: not-allowed;
}
</style>
