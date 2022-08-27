<template>
    <button class="btn">
        <slot></slot>
    </button>
</template>

<script>
    export default {
        name: 'MyButton'
    }
</script>

<style scoped>
    .btn {
        background: none;
        color: teal;
        padding: 10px 15px;
        cursor: pointer;
        border: 1px solid teal;
    }
</style>