<template>
    <section class="todoapp">
        <header class="header">
            <h1>todos</h1>
        </header>
        <section class="main">
            <input type="checkbox" id="toggle-all" class="toggle-all" v-model="allDone" />
            <label for="toggle-all" v-show="todos.length"></label>
            <input
                type="text"
                autofocus
                class="new-todo"
                v-model="newTodo"
                @keyup.enter="addTodo"
                placeholder="What needs to be done?"
            />
            <ul class="todo-list">
                <li v-for="todo in filteredTodos" :key="todo.id" :class="{ completed: todo.completed, editing: todo == editedTodo }">
                    <div class="view">
                        <input type="checkbox" class="toggle" v-model="todo.completed">
                        <label @dblclick="editTodo(todo)">{{ todo.title }}</label>
                        <button class="delete" @click="removeTodo(todo)"></button>
                    </div>
                    <input type="text" class="edit" v-model="todo.title" v-todo-focus="todo == editedTodo" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)">
                </li>
            </ul>
        </section>
        <footer class="footer" v-show="todos.length">
            <span class="todo-count">
                <strong>{{ remaining }}</strong> {{ remaining | pluralize }} left
            </span>
            <ul class="filters">
                <li>
                    <a href="#/all" :class="{ seleted: visibility == 'all' }">All</a>
                </li>
                <li>
                    <a href="#/active" :class="{ seleted: visibility == 'active' }">Active</a>
                </li>
                <li>
                    <a href="#/completed" :class="{ seleted: visibility == 'completed' }">Completed</a>
                </li>
            </ul>
            <button class="clear-completed" @click="removeCompleted" v-show="todos.length > remaining">Clear completed</button>
        </footer>
    </section>
</template>

<script>
const STORAGEKEY = 'todo_storage';
const todoStorage = {
    fetch: () => {
        const todos = JSON.parse(localStorage.getItem(STORAGEKEY)) || [];
        todos.forEach(function(todo, index) {
            todo.id = index;
        });
        todoStorage.uid = todos.length;
        return todos;
    },
    save: (todos) => {
        localStorage.setItem(STORAGEKEY, JSON.stringify(todos));
    }
};

const filters = {
    all: (todos) => {
        return todos;
    },
    active: (todos) => {
        return todos.filter((todo) => {
            return !todo.completed;
        });
    },
    completed: (todos) => {
        return todos.filter((todo) => {
            return todo.completed;
        });
    }
}

export default {
    name: "Todo",
    data: function() {
        return {
            todos: todoStorage.fetch(),
            newTodo: '',
            editedTodo: null,
            visibility: 'all'
        }
    },
    mounted: function () {
        window.addEventListener("hashchange", this.onHashChange);
        this.onHashChange();
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
        },
        editTodo: function(todo) {
            this.beforeEditCache = todo.title;
            this.editedTodo = todo;
        },
        doneEdit: function(todo) {
            if (!this.editedTodo) return;
            
            this.editedTodo = null;
            todo.title = todo.title.trim();
            if (!todo.title) this.removeTodo(todo);
        },
        cancelEdit: function(todo) {
            this.editedTodo = null;
            todo.title = this.beforeEditCache;
        },
        removeTodo: function (todo) {
            this.todos.splice(this.todos.indexOf(todo), 1);
        },
        onHashChange: function () {
            var visibility = window.location.hash.replace(/#\/?/, "");
            if (filters[visibility]) {
                this.visibility = visibility;
            } else {
                window.location.hash = "";
                this.visibility = "all";
            }
        },
        removeCompleted: function () {
            this.todos = filters.active(this.todos);
        }
    },
    computed: {
        filteredTodos: function() {
            return filters[this.visibility](this.todos);
        },
        remaining: function() {
            return filters.active(this.todos).length;
        },
        allDone: {
            get: function () {
                return this.remaining === 0;
            },
            set: function (value) {
                return this.todos.forEach((todo) => {
                    todo.completed = value;
                });
            }
        }
    },
    filters: {
        pluralize: function(n) {
            return n === 1 ? "item" : "items";
        }
    },
    directives: {
        "todo-focus": function(el, binding) {
            if (binding.value) el.focus();
        }
    }
}
</script>

<style lang="scss">
.todoapp {
    width: 100%;
    max-width: 500px;
    margin: auto;
    text-align:center;

    .header h1 {
        width: 100%;
        font-size: 100px;
        font-weight: 100;
        text-align: center;
        color: rgba(175, 47, 47, 0.15);
    }

    .main {
        box-shadow: 2px 0px 4px 0px rgba(0, 0, 0, 0.2), -2px 0px 4px 0px rgba(0, 0, 0, 0.2);
        background: #fff;
        position: relative;

        .toggle-all {
            position: absolute;
            opacity: 0;

            & + label {
                position: absolute;
                transform: rotate(90deg);
                height: 30px;
                width: 30px;
                left: 5px;
                top: 10px;
                color: #e6e6e6;

                &::after {
                    content: '❯';
                    font-size: 24px;
                    color: inherit;
                }
            }

            &:checked + label {
                color: #737373;
            }
        }

        .new-todo {
            padding: 15px 15px 15px 60px;
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
            border-top: 1px solid #ededed;

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

                .edit {
                    display: none;
                    position: relative;
                    margin: 0;
                    font-size: 24px;
                    line-height: 1.4em;
                    border: 0;
                    padding: 6px;
                    border: 1px solid #999;
                    box-shadow: inset 0 -1px 5px 0 rgba(0, 0, 0, 0.2);
                    box-sizing: border-box;
                    padding: 12px 16px;
                    width: calc(100% - 43px);
                    margin-left: 43px;
                }

                &.editing {
                    .view {
                        display: none;
                    }

                    .edit {
                        display: block;
                    }
                }

                .delete {
                    display: none;
                    position: absolute;
                    top: 0;
                    bottom: 0;
                    right: 10px;
                    margin: auto 0;
                    width: 40px;
                    height: 40px;
                    border: none;
                    color: #cc9a9a;
                    background: none;
                    font-size: 30px;

                    &:focus {
                        outline: none;
                    }

                    &:after {
                        content: 'x';
                    }
                }

                &:hover .delete {
                    display: block;
                }
            }
        }
    }

    .footer {
        padding: 10px 15px;
        background: #fff;
        color: #777;
        height: 20px;
        box-shadow: 2px 2px 4px 0px rgba(0, 0, 0, 0.2), -2px 2px 4px 0px rgba(0, 0, 0, 0.2);
        position: relative;

        &::before {
            content: '';
            position: absolute;
            right: 0;
            bottom: 0;
            left: 0;
            height: 50px;
            overflow: hidden;
            box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2), 0 8px 0 -3px #f6f6f6, 0 9px 1px -3px rgba(0, 0, 0, 0.2), 0 16px 0 -6px #f6f6f6, 0 17px 2px -6px rgba(0, 0, 0, 0.2);
        }

        .todo-count {
            float: left;

            strong {
                font-weight: 300;
            }
        }

        .filters {
            list-style: none;
            margin: 0;
            padding: 0;
            position: absolute;
            right: 0;
            left: 0;

            li {
                display: inline;

                a {
                    color: inherit;
                    margin: 3px;
                    padding: 3px 7px;
                    text-decoration: none;
                    border: 1px solid transparent;
                    border-radius: 3px;

                    &:hover,
                    &.seleted {
                        border-color: rgba(175, 47, 47, 0.2);
                    }
                }
            }
        }

        .clear-completed {
            float: right;
            text-decoration: none;
            cursor: pointer;
            position: relative;

            &:hover {
                text-decoration: underline !important;
            }
        }
    }
}
</style>