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
            <ul class="todo-list">
                <li v-for="todo in filteredTodo" :key="todo.id" :class="{ completed: todo.completed }">
                    <div class="view">
                        <input type="checkbox" class="toggle" v-model="todo.completed">
                        <label for="">{{ todo.title }}</label>
                    </div>
                </li>
            </ul>
        </section>
    </section>
</template>

<script>
const STORAGEKEY = 'todo_storage';
const todoStorage = {
    fetch: () => {
        return JSON.parse(localStorage.getItem(STORAGEKEY)) || [];
    },
    save: (todos) => {
        localStorage.setItem(STORAGEKEY, JSON.stringify(todos));
    }
};

export default {
    name: "Todo",
    data: function() {
        return {
            todos: todoStorage.fetch(),
            newTodo: ''
        }
    },
    watch: {
        todos: {
            handler: function(val) {
                todoStorage.save(val);
            },
            deep: true
        }
    },
    methods: {
        addTodo: function() {
            const value = this.newTodo && this.newTodo.trim();
            if (!value) return;

            this.todos.push({
                id: this.todos.length + 1,
                title: value,
                completed: false
            });
            this.newTodo = "";
        }
    },
    computed: {
        filteredTodo: function() {
            return this.todos;
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
        background: #fff;

        .new-todo {
            padding: 16px;
            width: 100%;
            border: none;
            box-sizing: border-box;

            &:focus {
                outline: none;
            }
        }

        .todo-list {
            margin: 0;
            padding: 0;
            list-style: none;

            li {
                position: relative;
                font-size: 24px;
                border-bottom: 1px solid #ededed;

                .toggle {
                    position: absolute;
                    width: 40px;
                    height: auto;
                    appearance: none;
                    top: 0;
                    left: 0;
                    bottom: 0;

                    & + label {
                        background-image: url('data:image/svg+xml;utf8,%3Csvg%20xmlns%3D%22http%3A//www.w3.org/2000/svg%22%20width%3D%2240%22%20height%3D%2240%22%20viewBox%3D%22-10%20-18%20100%20135%22%3E%3Ccircle%20cx%3D%2250%22%20cy%3D%2250%22%20r%3D%2250%22%20fill%3D%22none%22%20stroke%3D%22%23ededed%22%20stroke-width%3D%223%22/%3E%3C/svg%3E');
                        background-repeat: no-repeat;
                        background-position: center left;
                    }

                    &:checked + label {
                        background-image: url('data:image/svg+xml;utf8,%3Csvg%20xmlns%3D%22http%3A//www.w3.org/2000/svg%22%20width%3D%2240%22%20height%3D%2240%22%20viewBox%3D%22-10%20-18%20100%20135%22%3E%3Ccircle%20cx%3D%2250%22%20cy%3D%2250%22%20r%3D%2250%22%20fill%3D%22none%22%20stroke%3D%22%23bddad5%22%20stroke-width%3D%223%22/%3E%3Cpath%20fill%3D%22%235dc2af%22%20d%3D%22M72%2025L42%2071%2027%2056l-4%204%2020%2020%2034-52z%22/%3E%3C/svg%3E');
                    }
                }

                label {
                    text-align: left;
                    word-break: break-all;
                    padding: 15px 15px 15px 60px;
                    display: block;
                    line-height: 1.2;
                    transition: color 0.4s;
                }

                &.completed label {
                    text-decoration: line-through;
                }
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