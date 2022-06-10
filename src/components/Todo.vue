<template>
  <div v-if="currentTodo" class="edit-form">
    <h4>待办事宜</h4>
    <form>
      <div class="form-group">
        <label for="title">任务：</label>
        <input type="text" class="form-control" id="title"
          v-model="currentTodo.title"
        />
      </div>
      <div class="form-group">
        <label for="description">详情：</label>
        <input type="text" class="form-control" id="description"
          v-model="currentTodo.description"
        />
      </div>

      <div class="form-group">
        <label><strong>状态:</strong></label>
        {{ currentTodo.done ? "done" : "undone" }}
      </div>
    </form>

    <button class="badge badge-primary mr-2"
      v-if="currentTodo.done"
      @click="updatedone(false)"
    >
      未完成
    </button>
    <button v-else class="badge badge-primary mr-2"
      @click="updatedone(true)"
    >
      完成
    </button>

    <button class="badge badge-danger mr-2"
      @click="deleteTodo"
    >
      删除
    </button>

    <button type="submit" class="badge badge-success"
      @click="updateTodo"
    >
      更新
    </button>
    <p>{{ message }}</p>
  </div>

  <div v-else>
    <br />
    <p>Please click on a Todo...</p>
  </div>
</template>

<script>
import TodoDataService from "../services/TodoDataService";

export default {
  name: "todo",
  data() {
    return {
      currentTodo: null,
      message: ''
    };
  },
  methods: {
    getTodo(id) {
      TodoDataService.get(id)
        .then(response => {
          this.currentTodo = response.data;
          console.log(response.data);
        })
        .catch(e => {
          console.log(e);
        });
    },

    updatedone(status) {
      var data = {
        id: this.currentTodo.id,
        title: this.currentTodo.title,
        description: this.currentTodo.description,
        done: status
      };

      TodoDataService.update(this.currentTodo.id, data)
        .then(response => {
          console.log(response.data);
          this.currentTodo.done = status;
          this.message = '状态修改成功。';
        })
        .catch(e => {
          console.log(e);
        });
    },

    updateTodo() {
      TodoDataService.update(this.currentTodo.id, this.currentTodo)
        .then(response => {
          console.log(response.data);
          this.message = '信息更新成功';
        })
        .catch(e => {
          console.log(e);
        });
    },

    deleteTodo() {
      TodoDataService.delete(this.currentTodo.id)
        .then(response => {
          console.log(response.data);
          this.$router.push({ name: "todos" });
        })
        .catch(e => {
          console.log(e);
        });
    }
  },
  mounted() {
    this.message = '';
    this.getTodo(this.$route.params.id);
  }
};
</script>

<style>
.edit-form {
  max-width: 300px;
  margin: auto;
}
</style>
