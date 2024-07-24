<template>
    <button @click="onClick" :class="variant">
        <slot></slot>
    </button>
</template>
<script setup lang="ts">
const props = defineProps<{
    onClick: () => void;
    variant: 'primary' | 'secondary';
}>();
</script>
<style scoped>
button {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}
.primary {
    background-color: var(--primary-color);
    color: white;
}
.primary:hover {
    background-color: var(--primary-hightlight-color);
}
.secondary {
    background-color: var(--secondary-color);
    color: white;
}
.secondary:hover {
    background-color: var(--secondary-hightlight-color);
}
</style>