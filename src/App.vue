<template>
  <div id="app">
    <header-component list="list"></header-component>

    <div class="mainer">
      <div class="todo-grid">
        <div class="cell" v-for="(category, index) in categories" :key="index">
          <h3>
            <font-awesome-icon
              icon="trash"
              class="remove"
              @click="categoryDelete(category, index)"
            />
            {{category}} {{completedCount(filterByCategory(list, category))}}
          </h3>

          <transition-group tag="ul" class="todo-list" name="list">
            <li v-for="(task, index) in filterByCategory(list, category)" :key="index">
              <input class="done" type="checkbox" v-model="task.completed" />
              <s v-if="task.completed">{{task.name}}</s>
              <span v-else>{{task.name}}</span>
              <div class="buttons-block">
                <button class="btn" :disabled="task.completed" @click="taskEdit(task)" title="pen">
                  <font-awesome-icon icon="pen" />
                </button>
                <button class="btn" @click="taskDelete(task.uuid)" title="Delete">
                  <font-awesome-icon icon="trash" />
                </button>
              </div>
            </li>
          </transition-group>
        </div>
        <div class="cell empty"></div>
        <div class="cell empty"></div>
      </div>

      <new-task-form :newTask="newTask" :categories="categories" v-on:emitNewTask="taskAdd($event)"></new-task-form>

      <div class="todo-form">
        <div class="element">
          <input
            type="text"
            v-model.trim="newCategoryName"
            :class="{error: newCategoryError}"
            @keyUp="newCategoryError = false"
            placeholder="Enter name"
          />
        </div>
        <div class="element for-button">
          <button class="btn" @click="categoryAdd()" :disabled="!newCategoryName">
            <font-awesome-icon icon="plus" />
            <span>Add newcategory</span>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Header from "./components/Header";
import NewTaskForm from "./components/NewTaskForm";

export default {
  name: "app",
  components: {
    "header-component": Header,
    "new-task-form": NewTaskForm
  },
  data() {
    return {
      list: [],
      categories: [],
      newTask: {
        name: null,
        category: null
      },
      newCategoryName: null,
      newCategoryError: false
    };
  },
  methods: {
    taskAdd(newTask) {
      this.list.push(newTask);
      this.newTask = {
        name: null,
        category: null
      };
    },
    taskDelete(uuid) {
      if (confirm("Are you sure?")) {
        const i = this.list.findIndex(p => p.uuid === uuid);
        this.list.splice(i, 1);
      }
    },
    taskEdit(task) {
      task.name = prompt("Enter name", task.name) || task.name;
    },
    filterByCategory(list, filter) {
      return list.filter(task => task.category === filter);
    },
    completedCount(list) {
      if (list.length) {
        let count = 0;
        for (let task of list) {
          if (task.completed) {
            count++;
          }
        }
        return `(${count}/${list.length})`;
      }
    },
    categoryAdd() {
      if (this.categories.indexOf(this.newCategoryName.toLowerCase()) < 0) {
        this.categories.push(this.newCategoryName.toLowerCase());
        this.newCategoryName = null;
      } else {
        this.newCategoryError = true;
      }
    },
    categoryDelete(categoryName, i) {
      if (confirm("Are you sure?")) {
        this.categories.splice(i, 1);

        for (let task of this.list) {
          if (task.category === categoryName) {
            this.list.splice(this.list.indexOf(task), 1);
          }
        }
      }
    }
  },
  watch: {
    list: {
      handler: function() {
        localStorage.setItem("todoList", JSON.stringify(this.list));
      },
      deep: true
    },
    categories: function() {
      localStorage.setItem("categoryList", JSON.stringify(this.categories));
    }
  },
  created: function() {
    this.list = JSON.parse(localStorage.getItem("todoList")) || [];
    this.categories = JSON.parse(localStorage.getItem("categoryList")) || [];
  }
};
</script>

<style lang="scss">
@import "./styles/main.scss";
.list-enter-active,
.list-leave-active,
.list-move {
  transition-duration: 0.5s;
}

.list-enter {
  opacity: 0;
  transform: scale(0);
  transform-origin: center;
}

.list-enter-to {
  opacity: 1;
  transform: scale(1);
}

.list-leave-to {
  opacity: 0;
  transform: translateY(-600px);
}
</style>
