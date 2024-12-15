<template>
    <div class="modal" v-if="isVisible">
        <div class="modal-content">
            <h2>Félicitations !</h2>
            <p>Vous avez complété le sudoku avec succès !</p>
            <button @click="onRestart">Recommencer</button>
        </div>
    </div>
</template>

<script lang="ts">
export default {
    name: 'VictoryModal',
    props: {
        isVisible: {
            type: Boolean,
            required: true,
        },
    },
    emits: ['restart'],
    methods: {
        onRestart() {
            this.$emit('restart');
        },
    },
};
</script>

<style scoped>
.modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 3;
}

.modal-content {
    background: black;
    padding: 20px;
    text-align: center;
    border-radius: 5px;
}

button {
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
    margin-top: 20px;
}
</style>
