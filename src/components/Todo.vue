<template>
    <section class="todoapp">
        <header class="header">
            <h1>todos</h1>
        </header>
        <section class="main">
            <input
                type="text"
                autofocus
                class="new-todo"
                v-model="newTodo"
                @keyup.enter="addTodo"
                placeholder="What needs to be done?"
            />
        </section>
    </section>
</template>

<script>
export default {
    name: "Todo",
    data: function() {
        return {
            todos: [],
            newTodo: ''
        }
    },
    methods: {
        addTodo: function() {
            this.todos.push(this.newTodo);
            this.newTodo = "";
            console.log(this.todos);
        }
    }
}
</script>

<style lang="scss">
.todoapp {
    .header h1 {
        width: 100%;
        font-size: 100px;
        font-weight: 100;
        text-align: center;
        color: rgba(175, 47, 47, 0.15);
    }

    .main {
        box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 25px 50px 0 rgba(0, 0, 0, 0.1);

        .new-todo {
            padding: 16px;
            width: 100%;
            border: none;
            box-sizing: border-box;

            &:focus {
                outline: none;
            }
        }
    }
}
section {
    width: 100%;
    max-width: 500px;
    margin: auto;
    text-align:center;
}
</style>