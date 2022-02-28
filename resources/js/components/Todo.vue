<template>
    <div class="container">
        <div class="row justify-content-center my-5">
            <div class="col-md-6">
                <div class="card">
                    <div class="card-header">Todo</div>

                    <div class="card-body">
                        <div class="input-group">
                            <input type="text" placeholder="Todo..." class="form-control" aria-label="todo" aria-describedby="tod" v-model="todo_input">
                            <div class="input-group-append">
                                <button v-if="!edit_todo_id" type="submit" @click="saveTodo()" class="btn btn-info"> Add </button>
                                <button v-else type="submit" @click="updateTodo()" class="btn btn-info"> Update </button>
                            </div>
                        </div>
                         <button @click="resetTodo()" class="btn btn-sm text-danger float-right">Reset</button>
                        <table class="table table-bordered mt-5">
                            <thead>
                                <th>S No</th>
                                <th> Name </th>
                                <th> Actions </th>
                            </thead>
                            <tbody>

                            <tr v-for="(todo, index) in todos" :key="index">
                                <td> {{ ++index }} </td>
                                <td> {{ todo.name }} </td>
                                <td>
                                    <button @click="deleteTodo(--index)" class="btn btn-sm btn-danger">Delete</button>
                                    <button @click="editTodo(--index)" class="btn btn-sm btn-info">Edit</button>

                                 </td>
                            </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                todo_input: '',
                todos: [],
                api: 'http://localhost:8000/api/todos',
                edit_todo_id: '',
                edit_index: ''
            }
        },
        mounted() {
            this.axios.get( this.api ).then( res => {
                this.todos = res.data;
            })
        },
        methods: {
            saveTodo() {
                if ( this.todo_input.length > 0 ) {
                    let data = {name: this.todo_input}
                    this.axios.post( this.api, data ).then( res => {
                        this.todos.push( res.data )
                        this.todo_input = ''
                    })
                }
            },
            deleteTodo(index) {
                if (this.todos[index].id) {
                    this.axios.delete( this.api + '/' + this.todos[index].id ).then( res => {
                        this.todos.splice(index, 1)
                    })
                }
            },
            editTodo(index) {
                if(this.todos[index].id) {
                    this.todo_input = this.todos[index].name
                    this.edit_todo_id = this.todos[index].name
                    this.edit_index = index
                }
            },
            updateTodo() {
                 if ( this.todo_input.length > 0 ) {
                    let data = {name: this.todo_input}
                    this.axios.put( this.api + '/' + this.todos[this.edit_index].id , data ).then( res => {
                        this.todos[this.edit_index].name = res.data.name
                        this.resetTodo()
                    })
                }

            },
            resetTodo() {
                this.todo_input = ''
                this.edit_index = ''
                this.edit_todo_id = ''
            }
        }
    }
</script>
