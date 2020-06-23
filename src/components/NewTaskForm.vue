<template>
  <div class="todo-form">
    <div class="element">
      <input type="text" v-model.trim="newTask.name" placeholder="Enter name" />
    </div>
    <div class="element">
      <select v-model="newTask.category">
        <option value disabled hidden>Select category</option>
        <option
          v-for="(category, index) in categories"
          v-bind:value="category"
          :key="index"
        >{{category}}</option>
      </select>
    </div>
    <div class="element for-button">
      <button class="btn" @click="taskAdd(newTask)" :disabled="!newTask.name || !newTask.category">
        <font-awesome-icon icon="plus" />
        <span>Add new task</span>
      </button>
    </div>
  </div>
</template>

<script>
import { uuid } from "vue-uuid";

export default {
  props: ["newTask", "categories"],
  data() {
    return {};
  },
  methods: {
    taskAdd(task) {
      task.uuid = uuid.v1();
      this.$emit("emitNewTask", task);
    }
  }
};
</script>