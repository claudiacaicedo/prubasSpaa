<template>
    <div>
        <nav class="navbar navbar-light bg-light">
            <a href="/" class="navbar-brand">MEVN STACK</a>
        </nav>
        <div class="container">
            <div class="row pt-5">
                <div class="col-md-5">
                    <div class="card">
                        <div class="card-body">
                            <form @submit.prevent="sendTask">
                                <div class="form-group">
                                    <input type="text" 
                                    v-model="task.title"
                                    placeholder="Inserta una tarea"
                                    class="form-control">
                                </div>
                                <div class="form-group">
                                    <textarea v-model="task.description"
                                    cols="30" rows="10" 
                                    class="form-control"
                                    placeholder="Inserte la descripcion"></textarea>
                                </div>
                                <template v-if="edit === false">
                                    <button class="btn btn-primary btn-block">Enviar</button>
                                </template>
                                <template v-else>
                                    <button class="btn btn-primary btn-block">Actualizar</button>
                                </template>
                            </form>
                        </div>
                    </div>
                </div>
                <div class="col-md-7">
                    <table class="table table-bordered">
                        <thead>
                            <tr>
                                <th>Tareas</th>
                                <th>Descripcion</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="task of tasks" >
                                <td>{{task.title}} </td>
                                <td>{{task.description}} </td>
                                <td>
                                    <button @click="deleteTask(task._id)"
                                        class="btn btn-danger">
                                        Eliminar
                                    </button>
                                    <button  @click="editTask(task._id)"
                                     class="btn btn-secondary">
                                        Editar
                                    </button>
                                </td>

                            </tr>

                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    class Task{
        constructor(title, description) {
            this.title = title;
            this.description = description;
        }
    }
    
    export default {
    data(){
        return{
            task: new Task(),
            tasks: [],
            edit: false,
            taskToEdit: ''
        }
    },
    created() {
        this.getTasks();
    },
    methods:{
        
        sendTask() {
           if(this.edit === false){
                fetch('/api/tasks', {
                    method: 'POST',
                    body: JSON.stringify(this.task),
                    headers: {
                        'Accept': 'application/json',
                        'Content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.getTasks();
                    
                })
           } else {
               fetch('/api/tasks/' + this.taskToEdit, {
                   method: 'PUT',
                   body: JSON.stringify(this.task),
                   headers: {
                        'Accept': 'application/json',
                        'Content-type': 'application/json'
                    }
               })
               .then(res => res.json())
               .then(data => {
                   this.getTasks();
                   this.edit = false;
               });
           }

            this.task = new Task();
        },
        getTasks(){
            fetch('/api/tasks')
            .then(res => res.json())
            .then(data => {
                this.tasks = data;
                console.log(this.tasks) 
            })
        },
        deleteTask(id){
            fetch('/api/tasks/'+ id,{
                method: 'DELETE',
                headers: {
                    'Accept': 'application/json',
                    'Content-type': 'applicationjson'
                }
            })
            .then(res => res.json())
            .then(data => {
                this.getTasks();
            });
        },
        editTask(id){
            fetch('/api/tasks/'+ id)
            .then(res => res.json())
            .then(data => {
                this.task = new Task(data.title, data.description);
                this.taskToEdit = data._id;
                this.edit = true;
            });
        }
    }
}
</script>
