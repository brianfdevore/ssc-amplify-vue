<template>
    <div class="todo-item">
        <p>
            <input type="checkbox" v-bind:checked="todo.done" v-on:change="markComplete">
            <button class="icon-class" v-on:click="toggle_desc"><i v-bind:class="icon" aria-hidden="true"></i></button>
            <input type="text" class="name" v-model="todo.name" v-on:change="updateTodo" v-bind:class="{'is-complete':todo.done}">
            <button @click="$emit('del-todo', todo.id)" class="del"><i class="fas fa-trash"></i></button>
            <!-- <button @click="$emit('del-todo', todo.id)" class="del">X</button> -->
        </p>
        <p class="collapsible" v-show="!collapsed">
            <textarea type="text" class="desc" v-model="todo.description" v-on:change="updateTodo"/>
        </p>
    </div>
</template>

<script>
export default {
    name: 'TodoItem',
    data: function () {
        return {
            collapsed: true,
            icon: 'fa fa-plus'
        }
    },
    props: ['todo'],
    methods: {
        markComplete(todo) {
            this.todo.done = !this.todo.done;
            this.$emit('upd-todo', todo);
        },
        updateTodo(todo) {
            this.$emit('upd-todo', todo);
        },
        toggle_desc() {
            if(this.collapsed) {
                this.icon = 'fa fa-minus';
            }
            else {
                this.icon = 'fa fa-plus';
            }
        this.collapsed = !this.collapsed;
        }
    }
}
</script>

<style scoped>
.todo-item {
    /*background: #f4f4f4;*/
    padding: 2px;
    border-bottom: 2px #ccc dotted;
    text-align: left;
}

.is-complete {
    text-decoration: line-through;
}

.del {
    /* background: #ff0000; */
    color: #000;
    border: none;
    padding: 5px 9px;
    /* border-radius: 50%; */
    cursor: pointer;
    float: right;
}
.desc {
    padding-left: 35px;
    font-size: .80em;
    border: none;
    width: 85%;
}
.name{
    border: none;
    width: 85%;
}
.name:focus {
    outline: none;
}
.desc:focus {
    outline: 1px solid #ccc;
}
.icon-class {
    border: none;
    font-size: 0.6em;
    vertical-align: middle;
}
</style>