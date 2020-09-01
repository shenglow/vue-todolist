<template>
    <div id="app">
        <Todo></Todo>
    </div>
</template>

<script>
import Todo from './components/Todo.vue';
import './scss/style.scss';

export default {
    name: 'App',
    components: {
        Todo
    }
}
</script>

<style lang="scss">
body {
    background: #f5f5f5;
}

input::placeholder {
    color: #e6e6e6;
}

input:focus {
    outline: none;
}

h1 {
    margin: 0.5rem 0;
}

button {
    margin: 0;
    padding: 0;
    border: 0;
    background: none;
    color: inherit;
}
</style>
