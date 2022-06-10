<template>
  <div class="list row">
    <div class="col-md-8">
      <div class="input-group mb-3">
        <input type="text" class="form-control" placeholder="搜索待办清单"
          v-model="title"/>
        <div class="input-group-append">
          <button class="btn btn-outline-secondary" type="button"
            @click="searchTitle"
          >
            Search
          </button>
        </div>
      </div>
    </div>
    <div class="col-md-6">
      <h4>待办清单</h4>
      <ul class="list-group">
        <li class="list-group-item"
          :class="{ active: index == currentIndex }"
          v-for="(todo, index) in todos"
          :key="index"
          @click="setActiveTodo(todo, index)"
        >
          {{ todo.title }}
        </li>
      </ul>

      <button class="m-3 btn btn-sm btn-danger" @click="removeAllTodos">
       删除所有清单
      </button>
    </div>
    <div class="col-md-6">
      <div v-if="currentTodo">
        <h4>待办详情</h4>
        <div>
          <label><strong>任务:</strong></label> {{ currentTodo.title }}
        </div>
        <div>
          <label><strong>详情:</strong></label> {{ currentTodo.description }}
        </div>
        <div>
          <label><strong>状态:</strong></label> {{ currentTodo.done ? "done" : "undone" }}
        </div>

        <router-link :to="'/todos/' + currentTodo.id" class="badge badge-warning">编辑</router-link>
      </div>
      <div v-else>
        <br />
        <p>点击右侧列表查看详情</p>
      </div>
    </div>
  </div>
</template>

<script>
import TodoDataService from "../services/TodoDataService";

export default {
  name: "todos-list",
  data() {
    return {
      todos: [],
      currentTodo: null,
      currentIndex: -1,
      title: ""
    };
  },
  methods: {
    retrieveTodos() {
      TodoDataService.getAll()
        .then(response => {
          this.todos = response.data;
          console.log(response.data);
        })
        .catch(e => {
          console.log(e);
        });
    },

    refreshList() {
      this.retrieveTodos();
      this.currentTodo = null;
      this.currentIndex = -1;
    },

    setActiveTodo(todo, index) {
      this.currentTodo = todo;
      this.currentIndex = todo ? index : -1;
    },

    removeAllTodos() {
      TodoDataService.deleteAll()
        .then(response => {
          console.log(response.data);
          this.refreshList();
        })
        .catch(e => {
          console.log(e);
        });
    },
    
    searchTitle() {
      TodoDataService.findByTitle(this.title)
        .then(response => {
          this.todos = response.data;
          this.setActiveTodo(null);
          console.log(response.data);
        })
        .catch(e => {
          console.log(e);
        });
    }
  },
  mounted() {
    this.retrieveTodos();
  }
};
</script>

<style>
.list {
  text-align: left;
  max-width: 750px;
  margin: auto;
}
</style>
