<template>
    <div>
        <button v-on:click="expandAll">Expand All</button>
        <button v-on:click="collapseAll">Collapse All</button>
        <div v-bind:key="todo.id" v-for="todo in todos">
            <TodoItem ref="collapsible" v-bind:collapseAll="collapseAll" v-bind:todo="todo" v-on:del-todo="$emit('del-todo', todo.id)" v-on:upd-todo="$emit('upd-todo', todo)"/>
        </div>
    </div>
</template>

<script>
import TodoItem from './TodoItem.vue'

export default {
    name: 'Todos',
    components: {
        TodoItem
    },
    props: ["todos"],
    methods: {
        collapseAll(){
            this.$refs.collapsible.map(c => c.collapsed = true)
        },
        expandAll(){
            this.$refs.collapsible.map(c => c.collapsed = false)
        }
    }    
}
</script>

<style scoped>
</style>