<template>
    <div>
        <h2>todos</h2>
        <ul>
            <li v-for="todo in todos" :key="todo.id">
                {{ todo.name }}
                <button @click="showEditModal(todo)">Edit</button>
                <button @click="deleteTodo(todo.id)">Delete</button>
            </li>
        </ul>
    </div>
    <form @submit.prevent="addTodo">
        <input type="text" v-model="newTodo" placeholder="New Todo"/>
        <button type="submit">Add</button>
    </form>


    <div class="modal" v-if="isShowEditModal">
        <div class="modal-content">
            <h3>Edit Todo</h3>
            <input type="text" v-model="editTodoName" placeholder="New Name" />
            <button @click="updateTodo">Update</button>
            <button @click="closeEditModal">Cancel</button>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        data() {
            return {
                todos:[],
                newTodo: '',
                isShowEditModal: false,
                editTodoName: '',
                editingTodoId: null
            }
        },
        mounted() {
            this.loadTodos();            
        },
        methods: {
            showEditModal(todo) {
                this.isShowEditModal = true;
                this.editTodoName = todo.name;
                this.editingTodoId = todo.id;
            }, 
            closeEditModal() {
                this.isShowEditModal = false;
                this.editTodoName = '';
                this.editingTodoId = null;
            },
            async loadTodos() {
                try {
                    let response = await axios.get('http://localhost:8000/api/tasks');
                    this.todos = response.data;
                    this.newTodo = '';
                } catch (err) {
                    console.log('load-todos-error:', err);
                }                
            },
            async deleteTodo(id) {
                try {
                    await axios.delete('http://localhost:8000/api/tasks/' + id);
                    this.todos = this.todos.filter(todo => todo.id !== id);
                    alert('Todo deleted!!');
                } catch (e) {
                    console.log('delete-error:', e);
                }
            },
            async updateTodo() {
                try {
                    let res = await axios.put('http://localhost:8000/api/tasks/' + this.editingTodoId, {name: this.editTodoName});
                    let updatedTodo = res.data;
                    let index = this.todos.findIndex(todo => todo.id === updatedTodo.id);
                    if (index !== -1) {
                        this.todos.splice(index, 1, updatedTodo);
                    }
                    this.closeEditModal();
                    alert('Todo updated!!');
                } catch (e) {
                    console.log('delete-error:', e);
                }
            },
            async addTodo() {
                try {
                    let res = await axios.post('http://localhost:8000/api/tasks', {name: this.newTodo});
                    this.todos.push(res.data);
                    this.newTodo = '';
                    alert('Todo added successfully!');
                } catch (e) {
                    console.log('add-todo-error:', e);
                }
            }
        }
    }

</script>