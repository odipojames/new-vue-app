<template>
<div class="container">
<Header @toggle-add-task='toggleAddTask' title="Task Tracker" :showAddTask="showAddTask" />
<div v-if="showAddTask">
<AddTask @add-task='addTask' />
</div>

<Tasks @delete-task='deleteTask' :tasks="tasks" />
<router-view></router-view>
<Footer/>
</div>
  
</template>

<script>
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'
import Tasks from './components/Tasks'
import AddTask from './components/AddTask'

export default {
  name: 'App',
  components: {
   Header,
   Tasks,
   AddTask,
   Footer
  },
  data(){
    return{
      tasks:[],
      showAddTask:false
    }
  },

  methods:{
    async fetchTasks(){
    const res = fetch('api/tasks');
    const data =  (await res).json();
    return data
    },
    async fetchTask(id){
    const res = fetch(`api/tasks${id}`);
    const data =  (await res).json();
    return data
    },

    toggleAddTask(){
       this.showAddTask = !this.showAddTask
    },
    async addTask(task){
      const res = await fetch('api/tasks',
      {
        method:'POST',
        headers:{
          'Content-type':'application/json',
         
        },
         body:JSON.stringify(task)

      }
      )
      const data =  await res.json()

      this.tasks=[...this.tasks,data]
    },
    async deleteTask(id){
      
      if(confirm('are you sure you want to delete this?')){
        const res =  await fetch(`api/tasks/${id}`,
        {
          method:'DELETE',
        })
        res.status ===200?
        this.tasks = this.tasks.filter((task)=>task.id !==id):
        alert('Error occured')
      }
    }
  },
 async created(){
   this.tasks = await this.fetchTasks();
  }
}
</script>

<style>
.container{
  
  max-width: 500px;
  margin: 30px auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
  
}
</style>
