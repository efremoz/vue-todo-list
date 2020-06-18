<template>
  <div>
    <h2>Todo List</h2>
    <router-link to="/">Home</router-link>
    <hr>
    <AddTodo
        @add-todo="addTodo"
    />
    <select v-model="filter">
      <option value="all">All</option>
      <option value="completed">Completed</option>
      <option value="not-completed">Not Completed</option>
    </select>
    <hr>
    <Loader v-if="loading" />
    <TodoList
        v-else-if="filteredTodos.length"
        v-bind:todos="filteredTodos"
        @remove-todo="removeTodo"
    />
    <p v-else>No todos!</p>
  </div>
</template>

<script>
//@ - путь от src
import TodoList from '@/components/TodoList'
import AddTodo from '@/components/AddTodo'
import Loader from '@/components/Loader'
export default {
  name: 'app',
  data() {
    return {
      todos: [],
      loading: true,
      filter: 'all'
    }
  },
  //Вызывается сразу после монтирования экземпляра
  mounted() {

    let strTodos = localStorage.getItem('localTodos');
    let objTodos = JSON.parse(strTodos);

    if(null !== objTodos && 0 !== objTodos.length) {

      this.timeout(objTodos);
      
    } else {

      fetch('https://jsonplaceholder.typicode.com/todos?_limit=10')
        .then(response => response.json())
        .then(json => {
          this.timeout(json);
        })
     }
  },
  //Вызывается после того, как виртуальный DOM был обновлён из-за изменения данных.
  updated() {
    localStorage.setItem('localTodos', JSON.stringify(this.todos));
    
  },
  // watch: {
  //   filter(value) {
  //     console.log(value)
  //   }
  // },
  //Вычисляемые свойства, которые будут подмешаны к экземпляру Vue. В геттерах и сеттерах this будет указывать на экземпляр Vue.
  computed: {
    filteredTodos() {
      if (this.filter === 'all') {
        return this.todos
      }

      if (this.filter === 'completed') {
        return this.todos.filter(t => t.completed)
      }

      if (this.filter === 'not-completed') {
        return this.todos.filter(t => !t.completed)
      }
    }
  },
  methods: {
    removeTodo(id) {
      this.todos = this.todos.filter(t => t.id !== id)
    },
    addTodo(todo) {
      this.todos.push(todo)
    },
    timeout(res) {
         setTimeout(() => {
            this.todos = res;
            this.loading = false
          }, 1000)

    }
  },
  components: {
    TodoList, AddTodo, Loader
  }
}
</script>