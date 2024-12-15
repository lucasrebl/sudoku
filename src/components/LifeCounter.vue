<template>
    <div class="life-counter">
        <p>Vies restantes: {{ remainingLives }}</p>
    </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
    name: "LifeCounter",
    props: {
        remainingLives: {
            type: Number,
            required: true,
        },
    },
});
</script>

<style scoped>
.life-counter {
    margin-top: 10px;
    padding-right: 200px;
}
</style>
