<template>
    <section>
        <h1>todos</h1>
        <input type="text" autofocus v-model="newTodo" @keyup.enter="addTodo"/>
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
            console.log(this.newTodo);
        }
    }
}
</script>

<style scoped>
section {
    width: 100%;
    max-width: 500px;
    margin: auto;
    text-align:center;
}

h1 {
    color: red;
}
</style>