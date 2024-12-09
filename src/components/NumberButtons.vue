<template>
    <div class="number-buttons">
        <button 
            v-for="num in numbers" 
            :key="num" 
            @click="onNumberClick(num)" 
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
    margin-left: 50px;
}

.number-button:hover {
    background-color: #e0e0e0;
}
</style>
