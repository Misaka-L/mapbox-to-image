<script setup lang="ts">
const model = defineModel<boolean>()
</script>

<template>
    <Teleport v-if="model" to="body">
        <div class="dialog-layout">
            <div class="dialog">
                <div class="dialog-title">
                    <span>Preview</span>
                    <button @click="() => model = false">Close</button>
                </div>
                <div class="dialog-content">
                    <slot>No content</slot>
                </div>
            </div>
        </div>
    </Teleport>
</template>

<style scoped>
.dialog-layout {
    position: fixed;
    left: 0;
    top: 0;
    z-index: 100;

    height: 100vh;
    width: 100vw;

    background-color: var(--color-border);

    display: flex;
    justify-content: center;
    align-items: center;
}

.dialog {
    background-color: var(--color-background);

    height: 40rem;
    width: 90rem;

    overflow: hidden;

    display: flex;
    flex-direction: column;

    border-radius: 1rem;

    .dialog-content {
        flex: 1;

        height: 100%;
        width: 100%;

        overflow: auto;
    }

    .dialog-title {
        display: flex;
        justify-content: space-between;
        align-items: center;

        font-size: 1.4rem;
        font-weight: bold;
        padding: 0.5rem 1rem;

        background-color: var(--color-background-soft);
    }
}
</style>