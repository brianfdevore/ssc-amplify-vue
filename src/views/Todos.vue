<template>
  <div class="todos">
    <h1>Welcome to the Todo page</h1> 
    <AddTodo v-on:add-todo="createTodo"/>
    <!-- <input type="checkbox" id="toggle_desc" v-model="isChecked">
    <label for="toggle_desc">Show descriptions</label> -->
    <Todos v-bind:todos="todos" v-on:del-todo="deleteTodo" v-on:upd-todo="updateTodo" />
  </div>
</template>

<script>
// @ is an alias to /src
import { API } from 'aws-amplify';
import { createTodo } from '../graphql/mutations';
import { updateTodo } from '../graphql/mutations';
import { deleteTodo } from '../graphql/mutations';
import { listTodos } from '../graphql/queries';
import { onCreateTodo } from '../graphql/subscriptions';
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
      const { owner_id, name, description, done } = item;
      // if (!text) return;
      const todo = { owner_id, name, description, done };
      this.todos = [...this.todos, todo];
      await API.graphql({
        query: createTodo,
        variables: {input: todo},
      });
      this.getTodos();    
    },
    async updateTodo(item) {
      const { id, owner_id, name, description, done } = item;
      // if (!text) return;
      const todo = { id, owner_id, name, description, done };
      //this.todos = [...this.todos, todo];
      console.log(todo)
      await API.graphql({
        query: updateTodo,
        variables: {input: todo},
      });
      this.getTodos();    
    },
    async deleteTodo(tdid) {
      console.log("Deleted:" + tdid)
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
      //this.todos = todos.data.listTodos.items; // no sorting
      // sort by name (asc)
      this.todos = todos.data.listTodos.items.sort(function(a, b){
        var x = a.name.toLowerCase();
        var y = b.name.toLowerCase();
        if (x < y) {return -1;}
        if (x > y) {return 1;}
        return 0;
      });
    },
    subscribe() {
      API.graphql({ query: onCreateTodo })
        .subscribe({
          next: (eventData) => {
            let todo = eventData.value.data.onCreateTodo;
            if (this.todos.some(item => item.name === todo.name)) return; // remove duplications
            this.todos = [...this.todos, todo];
            console.log("new" + JSON.stringify(eventData.value.data));
          }
        });
    }
  }
}
</script>
