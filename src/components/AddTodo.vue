<template>
    <div>
        <form @submit="addTodo">
            <input type="text" name="title" v-model="title" placeholder="Add Todo...">
            <input type="submit" value="Add Todo" class="btn">
        </form>
    </div>
</template>


<script>

export default {
    name: 'AddTodo',
    data() {
        return {
            title: ''
        }
    },
    methods: {
        addTodo(e) {
            e.preventDefault();
            const newTodo = {
                name: this.title,
                description: '',
                done: false
            }

            //Send form submit event up to parent
            this.$emit('add-todo', newTodo);
            this.title = '';
        }
    }
}
</script>


<style scoped>
    form {
        display: flex;        
        justify-content: center;
    }

    input [type="text"] {
        flex: 10;
        padding: 5px;
    }

    input [type="submit"] {
        flex: 2;
    }
</style>