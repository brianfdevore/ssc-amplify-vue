<template>
    <div>
        <form @submit="addTodo">
            <input type="text" name="title" v-model="title" placeholder="Add Todo...">
            <!-- <input type="textarea" name="desc" v-model="desc" placeholder="Add description..."> -->
            <input type="submit" value="Add Todo" class="btn">
        </form>
    </div>
</template>


<script>

export default {
    name: 'AddTodo',
    data() {
        return {
            title: '',
            desc: ''
        }
    },
    methods: {
        addTodo(e) {
            e.preventDefault();
            const newTodo = {
                owner_id: '1',
                name: this.title,
                description: this.desc,
                done: false
            }

            //Send form submit event up to parent
            this.$emit('add-todo', newTodo);
            this.title = '';
            this.desc = '';
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