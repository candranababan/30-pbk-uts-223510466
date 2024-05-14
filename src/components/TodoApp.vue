<template>
  <div class ="container-fluid" style="width: 40em">
    <div class="card m-3">
      <div class="card-header">
        <ul class="nav nav-tabs">
          <li class="nav-item" @click="selectedMenu = 'Todos'" :class="{ 'active': selectedMenu === 'Todos' }">
            <a class="nav-link" href="#">Todos</a>
          </li>
          <li class="nav-item" @click="selectedMenu = 'Post'" :class="{ 'active': selectedMenu === 'Post' }">
            <a class="nav-link" href="#">Post</a>
          </li>
        </ul>
      </div>
      <div class="card-body">
        <div v-if="selectedMenu === 'Todos'">
          <form @submit.prevent="addTodo">
         </form>
         </div>
      </div>
    </div>

    <!-- input -->
    
    <div class="d-flex">
      <input v-model="task" type="text" placeholder="Masukkan List" class="form-control">
      <button @click="submitTask" class="btn btn-warning rounded-0">TEKAN</button>
    </div>
    
    <!-- Task Table -->
    <table class="table table-bordered mt-5">
  <thead>
    <tr>
      <th scope="col">Task</th>
      <th scope="col">Status</th>
      <th scope="col" class="text-center">#</th>
      <th scope="col" class="text-center">#</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="(task, index) in tasks" :key="index">
      <td>
        <span :class="{'selesai': task.status === 'selesai'}">{{task.name}}</span>
      </td>
      <td style="width: 120px">
        <span @click="changeStatus(index)" class="pointer"
          :class="{'text-danger': task.status === 'melakukan',
          'text-warning': task.status === 'proses melakukan',
          'text-success': task.status === 'selesai'
          }"
        >
          {{ firstCharUpper(task.status) }}
        </span>
      </td>
      <td>
        <div class="text-center" @click="editTask(index)">
          <span class="fa fa-pen"></span>
        </div>
      </td>
      <ul class="list-group list-group-flush list-group-numbered">
            <li class="list-group-item" v-for="todo in filteredTodos" :key="todo.title">
              {{ todo.title }}
            </li>
          </ul>

      <div v-if="selectedMenu === 'Post'">
          <label for="userSelect">Select User:</label>
          <select id="userSelect" v-model="selectedUser">
            <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
          </select>
          <ul v-if="selectedUser">
            <li v-for="post in userPosts" :key="post.id">
              {{ post.title }}
            </li>
          </ul>
      </div>

      <td>
        <div class="text-center" @click="deleteTask(index)">
          <span class="fa fa-trash"></span>
        </div>
      </td>
    </tr>
  </tbody>
</table>
</div>  


</template>

<script>

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },

  data(){
    return {
      task: '',
      editedTask: null,
      availableStatuses: ['melakukan', 'proses melaksanakan', 'selesai'],

      tasks: []
    }
  },

  data2: () => ({
    newTodo: '',
    todos: [],
    filter: null,
    selectedMenu: 'Todos', // Default menu selection
    users: [], // Placeholder for users data
    selectedUser: null, // Selected user ID for posts
    userPosts: [] // Placeholder for user posts data
  }),


  methods: {
    submitTask(){
      if(this.task.length === 0) return;

      if(this.editedTask === null){
        this.tasks.push({
          name: this.task,
          status: 'melakukan'
        });
      }else{
        this.tasks[this.editedTask].name = this.task;
        this.editedTask = null;
      }

      this.task = '';
    },

    deleteTask(index){
      this.tasks.splice(index, 1);
    },

    editTask(index){
      this.task = this.tasks[index].name;
      this.editedTask = index;
    },

    changeStatus(index){
      let newIndex = this.availableStatuses.indexOf(this.tasks[index].status);
      if(++newIndex > 2) newIndex = 0;
      this.tasks[index].status = this.availableStatuses[newIndex];
    },

    firstCharUpper(str){
      return str.charAt(0).toUpperCase() + str.slice(1);
    },

    fetchUsers() {
      fetch('https://jsonplaceholder.typicode.com/users')
        .then(response => response.json())
        .then(data2 => this.users = data2)
        .catch(error => console.error('Error fetching users:', error));
    },

    fetchUserPosts(userId) {
      fetch(`https://jsonplaceholder.typicode.com/posts?userId=${userId}`)
        .then(response => response.json())
        .then(data2 => this.userPosts = data2)
        .catch(error => console.error(`Error fetching posts for user ${userId}:`, error));
    },
  
    watch: {
    selectedUser(newVal) {
      if (newVal) {
        this.fetchUserPosts(newVal);
      }
    }
  },
  created() {
    this.fetchUsers();
  },

  computed: {
    filteredTodos() {
      if (this.filter === 'done') {
        return this.todos.filter(todo => todo.done);
      } else if (this.filter === 'undone') {
        return this.todos.filter(todo => !todo.done);
      } else {
        return this.todos;
      }
    }
  }

    }
  }

</script>


<style scoped>
.pointer {
  cursor: pointer;
}
.finished {
  text-decoration: line-through;
}
</style>
