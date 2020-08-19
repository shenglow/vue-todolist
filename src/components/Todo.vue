<template>
    <section class="todoapp">
        <header class="header">
            <h1>todos</h1>
        </header>
        <section class="main">
            <input type="text" autofocus v-model="newTodo" @keyup.enter="addTodo" placeholder="What needs to be done?"/>
        </section>
    </section>
</template>

<script>
export default {
    name: "Todo",
    data: function() {
        return {
            newTodo: ''
        }
    },
    methods: {
        addTodo: function() {
            alert(this.newTodo);
        }
    }
}
</script>

<style>
.todoapp h1 {
    width: 100%;
    font-size: 100px;
    font-weight: 100;
    text-align: center;
    color: rgba(175, 47, 47, 0.15);
}
section {
    width: 100%;
    max-width: 500px;
    margin: auto;
    text-align:center;
}
</style>