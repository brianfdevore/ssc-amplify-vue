<template>
    <div class="todo-item">
        <p>
            <input type="checkbox" v-bind:checked="todo.done" v-on:change="markComplete">
            <input type="text" class="name" v-model="todo.name" v-on:change="updateTodo" v-bind:class="{'is-complete':todo.done}">
            <!-- {{todo.name}} -->
            <!-- <button @click="$emit('del-todo', todo.id)" class="del"><i class="fas fa-trash"></i></button> -->
            <button @click="$emit('del-todo', todo.id)" class="del">X</button>
        </p>
        <p style="padding-left: 35px; font-size: .75em;">
            <textarea type="text" class="desc" v-model="todo.description" v-on:change="updateTodo"/>
        </p>
    </div>
</template>

<script>
export default {
    name: 'TodoItem',
    props: ['todo'],
    methods: {
        markComplete(todo) {
            this.todo.done = !this.todo.done;
            this.$emit('upd-todo', todo);
        },
        updateTodo(todo) {
            this.$emit('upd-todo', todo);
        }
    }
}
</script>

<style scoped>
.todo-item {
    background: #f4f4f4;
    padding: 2px;
    border-bottom: 2px #ccc dotted;
    text-align: left;
}

.is-complete {
    text-decoration: line-through;
}

.del {
    background: #ff0000;
    color: #fff;
    border: none;
    padding: 5px 9px;
    border-radius: 50%;
    cursor: pointer;
    float: right;
}
.desc {
    padding: 0px;
    border: none;
    width: 85%;
}
.desc_content {
    display: none;
    overflow: hidden;
}
.name{
    border: none;
    width: 85%;
}
.name:focus, .desc:focus {
    outline: none;
}
</style>