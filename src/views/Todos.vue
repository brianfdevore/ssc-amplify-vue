<template>
  <div class="todos">
    <h1>Welcome to the Todo page</h1> 
    <AddTodo v-on:add-todo="createTodo"/>
    <Todos v-bind:todos="todos" v-on:del-todo="deleteTodo"/>
  </div>
</template>

<script>
// @ is an alias to /src
import { API } from 'aws-amplify';
import { createTodo } from '../graphql/mutations';
import { deleteTodo } from '../graphql/mutations';
import { listTodos } from '../graphql/queries';
import Todos from '../components/Todos.vue';
import AddTodo from '../components/AddTodo.vue';

export default {
  name: 'TodosView',
  async created() {
    this.getTodos();
  },
  data() {    
    return {
      todos: []
    }
  },
  components: {
    Todos,
    AddTodo
  }, 
  methods: {
    async createTodo(item) {
      const { name, done } = item;
      // if (!text) return;
      const todo = { name, done };
      this.todos = [...this.todos, todo];
      await API.graphql({
        query: createTodo,
        variables: {input: todo},
      });
      this.getTodos();    
    },
    async deleteTodo(tdid) {
      console.log(tdid)
      const todoDetails = {
        id: tdid,
      };
      this.todos = this.todos.filter(todo => todo.id !== tdid)
      await API.graphql({
        query: deleteTodo,
        variables: {input: todoDetails},
      });  
      //this.getTodos();    
    },
    async getTodos() {
      const todos = await API.graphql({
        query: listTodos,
      });
      this.todos = todos.data.listTodos.items;
    }
  }
}
</script>
