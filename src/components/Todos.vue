<template>
  <div class="row todo-content">
    <div class="col-md-6">
      <div class="todo-list not-done">
        <h1>TODO LIST</h1>
        <div class="input-group mb-3">
          <input type="text" class="form-control" placeholder="Enter content" v-model="textContent">
          <div class="input-group-append">
            <button class="btn btn-outline-secondary" type="button" id="button-addon2" @click="addTask()">Add</button>
          </div>
        </div>
        <hr>

        <ul class="list-unstyled" v-for="(todo) in todos" :key="todo.id">
          <li class="ui-state-default li-items mt-1">
            <div class="input-group">
              <div class="input-group-prepend">
                <div class="input-group-text">
                  <input type="checkbox" aria-label="Radio button for following text input" :checked="todo.checked" v-model="todo.checked">
                </div>
              </div>

              <input type="text" class="form-control" :class="{ 'done-task': todo.completed }" v-model="todo.content">

              <div class="input-group-append remove-icon">
                <span class="input-group-text" @click="delTask(todo.id)">&#10060;</span>
              </div>
            </div>
          </li>
        </ul>

        <hr>
        <div class="todo-footer row">
          <div class="col-md-6">
            <div class="form-check form-check-inline" @click="checkAll(1)">
              &#9989;
              <label class="form-check-label" for="inlineRadio1">Check all</label>
            </div>
            <div class="form-check form-check-inline" @click="checkAll(0)">
              &#10062;
              <label class="form-check-label" for="inlineRadio2">UnCheck all</label>
            </div>
          </div>
          <div class="col-md-6 save-all">
            <div class="form-check form-check-inline save-all">
              <button type="button" class="btn btn-success btn-sm" @click="doneAll()">DONE ALL &#10004;</button>
            </div>
            <div class="form-check form-check-inline save-all">
              <button type="button" class="btn btn-dark btn-sm" @click="delAll()">DEL ALL &#10006;</button>
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
  name: 'Todos',
  data: function () {
    return {
      todos: [],
      textContent: '',
      code: 2
    }
  },
  created () {
    axios.get('http://localhost:8088/api/index')
      .then(response => {
        this.todos = response.data.data
      })
  },
  methods: {
    addTask: function () {
      if (this.textContent.trim().length === 0) {
        return
      }

      let params = {
        'id': this.code,
        'content': this.textContent,
        'checked': false,
        'completed': false
      }

      axios.post('http://localhost:8088/api/add', params)
        .then(response => {
          this.todos = response.data.data
        })
    },
    delTask: function (index) {
      if (confirm('Bạn có chắc chắn muốn xoá không?')) {
        console.log(index)
        axios.post('http://localhost:8088/api/remove', { id: index })
          .then(response => {
            this.todos = response.data.data
          })
      }
    },
    checkAll: function (flag) {
      this.todos.forEach(todo => {
        todo.checked = flag
      })
    },
    doneAll: function () {
      if (confirm('Bạn có chắc chắn hoàn thành nhiệm vụ không?')) {
        let params = this.todos
        axios.post('http://localhost:8088/api/doneAll', { params })
          .then(response => {
            this.todos = response.data.data
            // console.log(response.data)
          })
      }
    },
    delAll: function () {
      if (confirm('Bạn có chắc chắn xoá nhiệm vụ không?')) {
        let params = this.todos
        axios.post('http://localhost:8088/api/removeAll', { params })
          .then(response => {
            this.todos = response.data.data
            // console.log(response.data)
          })
      }
    }
  }

}
</script>

<style scoped>
.input-group-text {
  display: block;
}
.todo-content {
    display: flex;
    justify-content: center;
  }
  .todo-list h1 {
    margin-top: 20px;
    padding-bottom: 20px;
    text-align: center;
    font-weight: bold;
  }
  .todo-footer {
    background-color: #d2e8ca;
    padding: 10px 20px 15px;
  }
  #done-items li {
    padding: 10px 0;
    border-bottom: 1px solid #ddd;
    text-decoration: line-through;
  }
  .done-task {
    text-decoration: line-through;
    color: orange;
  }
  .form-check-inline {
    cursor: pointer;
  }
  .remove-icon span {
    cursor: pointer;
  }
  .save-all {
    float: right;
  }
</style>
