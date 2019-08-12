<template>
  <div>
    <input type="text" class="todo-input" placeholder="需要做的事情"
           v-model="newTodo" @keyup.enter="addTodo"
    >
    <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
      <todo-item v-for="(todo, index) in todosFiltered"
                 :key="todo.id"
                 :todo="todo"
                 :index="index"
                 :checkAll="!anyRemaining"

      >
      </todo-item>
    </transition-group>

    <div class="extra-container">
      <todo-check-all :any-remaining="anyRemaining"></todo-check-all>
      <todo-items-remaining :remaining="remaining"></todo-items-remaining>
    </div>

    <div class="extra-container">
      <todo-filter></todo-filter>
      <div>
        <transition name="fade">
          <todo-clear-completed :showClearCompletedButton="showClearCompletedButton"></todo-clear-completed>
        </transition>

      </div>

    </div>
  </div>
</template>

<script>
    import TodoItem from "./TodoItem"
    import TodoItemsRemaining from "./TodoItemsRemaining"
    import TodoCheckAll from "./TodoCheckAll"
    import TodoFilter from './TodoFilter'
    import TodoClearCompleted from "./TodoClearCompleted"

    export default {
        name: "TodoList",
        components: {TodoClearCompleted, TodoFilter, TodoCheckAll, TodoItemsRemaining, TodoItem},
        data() {
            return {
                newTodo: '',
                idForTodo: 3,
                beforeEditCache: '',
                filter: 'all',
                todos: [
                    {
                        'id': 1,
                        'title': '学习vue',
                        'completed': false,
                        'editing': false,
                    },
                    {
                        'id': 2,
                        'title': '学习react',
                        'completed': false,
                        'editing': false,

                    }


                ]
            }
        },
        created() {
            eventBus.$on('removedTodo', (index) => this.remaining(index))
            eventBus.$on('finishedEdit', (data) => this.finishedEdit(data))
            eventBus.$on('checkAllChanged', (checked) => this.checkAllTodos(checked))
            eventBus.$on('filterChanged', (filter) => this.filter = filter)
            eventBus.$on('clearCompletedTodos', this.clearCompleted)
        },
        beforeDestroy() {
            eventBus.$off('removedTodo', (index) => this.remaining(index))
            eventBus.$off('finishedEdit', (data) => this.finishedEdit(data))
            eventBus.$off('checkAllChanged', (checked) => this.checkAllTodos(checked))
            eventBus.$off('filterChanged', (filter) => this.filter = filter)
            eventBus.$off('clearCompletedTodos', this.clearCompleted)
        },
        computed: {
            remaining() {
                return this.todos.filter(todo => !todo.completed).length
            },
            anyRemaining() {
                return this.remaining !== 0
            },
            todosFiltered() {
                if (this.filter === 'all') {
                    return this.todos
                } else if (this.filter === 'active') {
                    return this.todos.filter(todo => !todo.completed)

                } else if (this.filter === 'completed') {
                    return this.todos.filter(todo => todo.completed)
                }
            },
            showClearCompletedButton() {
                return this.todos.filter(todo => todo.completed).length > 0
            }
        },
        methods: {
            addTodo() {
                if (this.newTodo.trim().length === 0) {
                    return
                }
                this.todos.push({
                    id: this.idForTodo,
                    title: this.newTodo,
                    completed: false
                })
                this.newTodo = ''
                this.idForTodo++
            },
            removeTodo(index) {
                this.todos.splice(index, 1)
            },

            editTodo(todo) {
                this.beforeEditCache = todo.title
                todo.editing = true
            },
            doneEdit(todo) {
                if (todo.title.trim().length === 0) {
                    todo.title = this.beforeEditCache
                }
                todo.editing = false
            },
            cancelEdit(todo) {
                todo.title = this.beforeEditCache
                todo.editing = false
            },
            checkAllTodos() {
                this.todos.forEach((todo) => todo.completed = event.target.checked)
            },
            clearCompleted() {
                this.todos = this.todos.filter(todo => !todo.completed)
            },
            finishedEdit(data) {
                const index = this.todos.findIndex(item => item.id === data.id)
                this.todos.splice(index, 1, data)
            }
        }
    }
</script>

<style lang="scss">
  @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.2/animate.min.css");

  .todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;

    &:focus {
      outline: 0;
    }
  }

  .todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: .3s;
  }

  .remove-item {
    cursor: pointer;
    margin-left: 14px;

    &:hover {
      color: black;
    }
  }

  .todo-item-left {
    display: flex;
    align-items: center;
  }

  .todo-item-label {
    padding: 10px;
    border: 1px solid #fff;
    margin-left: 12px;
  }

  .todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;

    &:focus {
      outline: none;
    }
  }

  .completed {
    text-decoration: line-through;
    color: gray;
  }

  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgray;
    padding-top: 14px;
    margin-bottom: 14px;
  }

  button {
    font-size: 14px;
    background-color: white;
    appearance: none;

    &:hover {
      background: lightgreen;
    }

    &:focus {
      outline: none;
    }
  }

  .active {
    background: lightgreen;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
  }

</style>
