<template>
    <div class="container">
        <div class="form-row">
            <div class="col-row">
                <input type="text" class="form-control" @keyup="searchTask" v-model="q" placeholder="Rechercher une tache ...">
            </div>
        </div>
        <add-task @task-added="refresh"></add-task>
           <ul class="list-group">
            <li class="list-group-item d-flex justify-content-between align-items-center" v-for="task in tasks.data" :key="task.id">
                <a href="#">{{ task.name }}</a>
                <div>
                <!-- Button edit -->
                <button type="button" class="btn btn-success" data-toggle="modal" data-target="#editModal" @click="getTask(task.id)">
                Editer
                </button>
                <!-- Button delate -->
                <button type="button" class="btn btn-danger" @click="delateTask(task.id)">Delate</button>
                </div>
            </li>
                <edit-task  v-bind:taskToEdit="taskToEdit" @task-updated="refresh">

                </edit-task>            
           </ul>
           <pagination :data="tasks" @pagination-change-page="getResults" class="mt-5"></pagination>
    </div>
</template>

<script>
    export default {

        data() {
            return{
                tasks: {},
                taskToEdit: '',
                q: ''
            }
        },

        created() {     
            axios.get('http://localhost/todo/public/tasksList')
                .then(response => this.tasks = response.data)
                .catch(error => console.log(error));
        },

        methods: {
        
        getResults(page = 1) {
                axios.get('http://localhost/todo/public/tasksList?page=' + page)
                    .then(response => {
                        this.tasks = response.data;
                    });   
        },

        getTask(id) {
            axios.get('http://localhost/todo/public/tasks/edit/' + id)
                .then(response => this.taskToEdit = response.data)
                .catch(error => console.log(error));
        },

        delateTask(id) {
            axios.delete('http://localhost/todo/public/tasks/' + id)
                .then(response=> this.tasks = response.data)
                .catch(error => console.log(error));
        },

        searchTask() {

            if (this.q.length > 3) {
                axios.get('http://localhost/todo/public/tasksList/' + this.q)
                    .then(response => this.tasks =  response.data)
                    .catch(error => console.log(error));
            }else {
                axios.get('http://localhost/todo/public/tasksList')
                    .then(response => this.tasks = response.data)
                    .catch(error => console.log(error));
            }

        },

        refresh(tasks){
            this.tasks = tasks.data 
            }
        },

        mounted() {
            console.log('Component mounted.')
        }

    }
</script>