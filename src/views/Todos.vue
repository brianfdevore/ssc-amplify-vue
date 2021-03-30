<template>
  <div class="todos">
    <h1>Welcome to the Todo page</h1> 
    <AddTodo v-on:add-todo="createTodo"/>
    <Todos v-bind:todos="todos" v-on:del-todo="deleteTodo" v-on:upd-todo="updateTodo" />
  </div>
</template>

<script>
// @ is an alias to /src
import { Auth } from 'aws-amplify';
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
  async beforeCreate() {
    try {      
      // get jwtToken from provider
      // const currentSession = await Auth.currentSession();
      // this.jwtToken = currentSession.getAccessToken().getJwtToken();
      // console.log(this.jwtToken);

      const currentUser = await Auth.currentUserInfo();
      //console.log(currentUser.id);
      this.userId = currentUser.id;
    } catch (err) {
      this.userId = 1;
    }
  },
  async created() {
    this.getTodos();
  },
  data() {    
    return {
      todos: [],
      jwtToken: '',
      userId: ''
    }
  },
  components: {
    Todos,
    AddTodo
  },
  methods: {
    // async getjwtToden() {
    //   const currentSession = await Vue.prototype.$Amplify.Auth.currentSession();
    //   return currentSession.getAccessToken().getJwtToken();
    // },
    async createTodo(item) {
      const { owner_id, name, description, done } = item;
      // if (!text) return;
      const todo = { owner_id, name, description, done };
      todo.owner_id = this.userId;
      this.todos = [...this.todos, todo];
      await API.graphql({
        query: createTodo,
        variables: {input: todo},
      });
      this.getTodos();
      console.log("Created:", JSON.stringify(todo));
    },
    async updateTodo(item) {
      const { id, owner_id, name, description, done } = item;
      // if (!text) return;
      const todo = { id, owner_id, name, description, done };
      //this.todos = [...this.todos, todo];
      await API.graphql({
        query: updateTodo,
        variables: {input: todo},
      });
      this.getTodos();    
      console.log("Updated:", JSON.stringify(todo));
    },
    async deleteTodo(tdid) {
      const todoDetails = {
        id: tdid,
      };
      this.todos = this.todos.filter(todo => todo.id !== tdid)
      await API.graphql({
        query: deleteTodo,
        variables: {input: todoDetails},
      });  
      //this.getTodos(); 
      console.log("Deleted:", JSON.stringify(todoDetails));   
    },
    async getTodos() {
      if(this.userId == null || this.userId == '') {
        try {              
          // get jwtToken from provider
          // const currentSession = await Auth.currentSession();
          // this.jwtToken = currentSession.getAccessToken().getJwtToken();
          // console.log(this.jwtToken);
          const currentUser = await Auth.currentUserInfo();
          //console.log(currentUser.id);
          this.userId = currentUser.id;
        } catch (err) {
          this.userId = 1;
        }
      }
      let filter = {
          owner_id: {
              eq: this.userId
          }
      };
      const todos = await API.graphql({
        query: listTodos,
        variables: {filter: filter}
      });
      
      // no sorting
      //this.todos = todos.data.listTodos.items;

      // sort by todo.name alphabetically (asc)
      // this.todos = todos.data.listTodos.items.sort(function(a, b){
        // var x = a.name.toLowerCase();
        // var y = b.name.toLowerCase();
        // if (x < y) {return -1;}
        // if (x > y) {return 1;}
        //return 0;

        // boolean sort (2 groups)
        this.todos = todos.data.listTodos.items.sort(function(a, b){
        return (a.done === b.done)? 0 : a.done? 1 : -1; // <-- false (not done) values first
        // return (a.done === b.done)? 0 : a.done? -1 : 1; // <-- true (done) values first
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
