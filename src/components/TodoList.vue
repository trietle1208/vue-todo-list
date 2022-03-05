<template>
  <div class="row todo-content">
    <div class="col-md-6">
      <div class="todo-list not-done">
        <h1>TODO-LIST</h1>
        <div class="input-group mb-3">
          <input
            type="text"
            class="form-control"
            placeholder="Enter content"
            v-model="textContent"
          />
          <div class="input-group-append">
            <button
              class="btn btn-outline-secondary"
              type="button"
              id="button-addon2"
              @click="onAddTask"
            >
              Add
            </button>
          </div>
        </div>
        <hr />
        <ul class="list-unstyled" v-for="(todo) in todos" :key="todo.id">
          <li class="ui-state-default li-items mt-1">
            <div class="input-group">
              <div class="input-group-prepend">
                <div class="input-group-text">
                  <input
                    type="checkbox"
                    aria-label="Radio button for following text input"
                    :checked="todo.checked"
                    v-model="todo.checked"
                  />
                </div>
              </div>
              <input
                type="text"
                class="form-control"
                :class="{ 'done-task': todo.completed }"
                v-model="todo.content"
              />
              <div class="input-group-append remove-icon">
                <span class="input-group-text" @click="onDeleteTask(todo.id)"
                  >&#10060;</span
                >
              </div>
            </div>
          </li>
        </ul>
        <hr />
        <div class="todo-footer row">
          <div class="col-md-6">
            <div class="form-check form-check-inline" @click="onCheckAll(1)">
              &#9989;
              <label class="form-check-label" for="inlineRadio1">Check all</label>
            </div>
            <div class="form-check form-check-inline" @click="onCheckAll(0)">
              &#10062;
              <label class="form-check-label" for="inlineRadio2">UnCheck all</label>
            </div>
          </div>
          <div class="col-md-6 save-all">
            <div class="form-check form-check-inline save-all">
              <button type="button" class="btn btn-success btn-sm" @click="onDoneAll">
                DONE ALL &#10004;
              </button>
            </div>
            <div class="form-check form-check-inline save-all">
              <button type="button" class="btn btn-dark btn-sm" @click="onDeleteAll">DEL ALL &#10006;</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "TodoList",
  data: function () {
    return {
      todos: [],
      textContent: "",
      code: 2,
    };
  },
  created () {
    axios.get('http://127.0.0.1:8000/api/index')
      .then(response => {
        this.todos= response.data.data;
        console.log(this.todos);
      })
  },
  methods: {
    onAddTask() {
      if (this.textContent.trim().length === 0) {
        alert("Please Enter Tasks Name !!!");
      } else {
        let params = {
          id: this.code,
          content: this.textContent,
          checked: 0,
          completed: 0,
        }
        axios.post('http://127.0.0.1:8000/api/addTask', params)
          .then(response => {
            this.todos= response.data.data;
            this.textContent = '';
        })
      }
    },
    onDeleteTask(id) {
      if (confirm("Are you sure you want to delete this task?")) {
        axios.post('http://127.0.0.1:8000/api/deleteTask', {id : id})
          .then(response => {
            this.todos= response.data.data;
        })
      }
    },
    onCheckAll(flag) {
      this.todos.forEach((todo) => {
        todo.checked = flag;
      });
      
    },
    onDoneAll() {
      let params = this.todos;
      if (confirm("Are you sure to check these tasks are complete?")) {
          axios.post('http://127.0.0.1:8000/api/doneAllTask', {params})
          .then(response => {
            this.todos= response.data.data;
            // console.log(response.data);
        })
      }
    },
    onDeleteAll() {
      let params = this.todos;
      if (confirm("Are you sure to check these tasks are delete?")) {
          axios.post('http://127.0.0.1:8000/api/deleteAllTask', {params})
          .then(response => {
            this.todos= response.data.data;
        })
      }
    },
  },
};
</script>

<style scoped>
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
