<template>
  <div class="home">
    <h1 class="title">Home</h1>
    <hr>
    <div class="columns">
      <div class="column is-3 is-offset-3">

        <span v-if="success">{{ success }}</span>
        <span v-if="error">{{ error }}</span>

        <form @submit.prevent="addTask">
          <h2 class="subtitle">Add Task</h2>

          <div class="field">
            <label class="label">Description</label>
            <div class="control">
              <input type="text" class="input" v-model="description">
            </div>
          </div>

          <div class="field">
            <label class="label">Status</label>
            <div class="control">
              <div class="select">
                <select v-model="status">
                  <option value="todo">Todo</option>
                  <option value="done">Done</option>
                </select>
              </div>
            </div>
          </div>

          <div class="field">
            <div class="control">
              <button class="button is-link">Submit</button>
            </div>
          </div>

        </form>
      </div>
    </div>

    <div class="columns">
      <div class="column is-6">
        <h2 class="subtitle">Todo</h2>

        <div class="todo">
          <div v-for="task in tasks" :key="task.id">
            <div class="card" v-if="task.status == 'todo'">
              <div class="card-content">{{ task.description }}</div>

              <p>{{ task.status }}</p>

              <footer class="card-footer">
                <a class="card-footer-item" @click="setDone(task, 'done')">Done</a>
              </footer>
            </div>
          </div>
        </div>
      </div>

      <div class="column is-6">
        <h2 class="subtitle">Done</h2>

        <div class="done">
          <div v-for="task in tasks" :key="task.id">
            <div class="card" @click="setTodo(task, 'todo')" v-if="task.status == 'done'">
              <div class="card-content">{{ task.description }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Home',
  data() {
    return {
      description: '',
      status: 'todo',
      success: '',
      error: null,
      tasks: []
    }
  },
  methods: {
    async getTasks() {
      try {
        let response = await axios({
          method: 'get',
          url: `http://localhost:8000/tasks/`,
          auth: {
            username: 'admin',
            password: '123456'
          }
        })
        this.tasks = await response.data
      } catch (err) {
        this.error = err.message
      }
    },
    async addTask() {
      if (this.description) {
        try {
          let response = await axios({
            method: 'post',
            url: `http://localhost:8000/tasks/`,
            auth: {
              username: 'admin',
              password: '123456'
            },
            data: {
              description: this.description,
              status: this.status
            }
          })
          let { id, description, status } = await response.data
          await this.tasks.push({
            id, description, status
          })

          this.description = ''
          this.status = 'todo'
          this.success = 'Data has been saved!'

        } catch (err) {
          this.error = err.message
        }
      }
    },
    async setDone({ id, description }, status) {
      let response = await axios({
        method: 'put',
        url: `http://localhost:8000/tasks/${id}/`,
        headers: {
          'Content-Type': 'application/json'
        },
        auth: {
          username: 'admin',
          password: '123456'
        },
        data: {
          status,
          description
        }
      })
      let updatedTask = await response.data
      this.tasks = this.tasks.filter((task) => {
        return task.id !== id
      })
      this.tasks.push(updatedTask)
    },
    async setTodo(arg, newStatus) {
      let taskChosen = this.tasks.filter(task => task.id === arg.id)[0]
      console.log(taskChosen)
      let response = axios({
        method: 'put',
        url: `http://localhost:8000/tasks/${taskChosen.id}/`,
        headers: {
          'Content-Type': 'application/json'
        },
        auth: {
          username: 'admin',
          password: '123456'
        },
        data: {
          description: taskChosen.description,
          status: newStatus
        }
      })
      let result = await response.data
      taskChosen.status = result.status
    }
  },
  mounted() {
    this.getTasks()
  }
}
</script>

<style lang="scss">
.select, select {
  width: 100%;
}

.card {
  margin-bottom: 20px;
}

.done {
  opacity: 0.3;
}
</style>
