<template>
  <div class="task-list">
    <form @submit.prevent="addTask" class="task-form">
      <input
        v-model="newTaskTitle"
        class="input"
        placeholder="New task..."
        required
      />
      <select v-model="newTaskPriority" class="select">
        <option value="low">Low</option>
        <option value="medium">Medium</option>
        <option value="high">High</option>
      </select>
      <button type="submit" class="add-btn">Add</button>
    </form>

    <p class="task-count">Pending tasks: {{ pendingTasksCount }}</p>

    <transition-group name="list" tag="ul" class="task-items">
      <TaskItem
        v-for="task in sortedTasks"
        :key="task.id"
        :task="task"
        @delete-task="deleteTask"
        @toggle-complete="toggleComplete"
        @update-title="updateTaskTitle"
      />
    </transition-group>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, watch, nextTick } from "vue";
import TaskItem from "./TaskItem.vue";
import { Task } from "../interfaces/Task";

export default defineComponent({
  name: "TaskList",
  components: { TaskItem },
  setup() {
    const tasks = ref<Task[]>([]);

    const newTaskTitle = ref("");
    const newTaskPriority = ref<"low" | "medium" | "high">("low");

    const addTask = async () => {
      const newTask: Task = {
        id: Date.now(),
        title: newTaskTitle.value,
        priority: newTaskPriority.value,
        completed: false,
      };
      tasks.value.push(newTask);
      newTaskTitle.value = "";
      await nextTick();
      scrollToNewTask();
    };

    const deleteTask = (id: number) => {
      tasks.value = tasks.value.filter((task) => task.id !== id);
    };

    const toggleComplete = (id: number) => {
      const task = tasks.value.find((task) => task.id === id);
      if (task) task.completed = !task.completed;
    };

    const updateTaskTitle = ({ id, title }: { id: number; title: string }) => {
      const task = tasks.value.find((task) => task.id === id);
      if (task) task.title = title;
    };

    const sortedTasks = computed(() => {
      const priorityOrder = { high: 1, medium: 2, low: 3 };
      return [...tasks.value].sort(
        (a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]
      );
    });

    const pendingTasksCount = computed(() => {
      return tasks.value.filter((task) => !task.completed).length;
    });

    const scrollToNewTask = () => {
      const taskList = document.querySelector(".task-items");
      taskList?.scrollTo({
        top: taskList.scrollHeight,
        behavior: "smooth",
      });
    };

    watch(
      () => tasks.value,
      (newTasks, oldTasks) => {
        console.log("Task list updated:", newTasks);
      }
    );

    return {
      tasks,
      newTaskTitle,
      newTaskPriority,
      addTask,
      deleteTask,
      toggleComplete,
      updateTaskTitle,
      sortedTasks,
      pendingTasksCount,
    };
  },
});
</script>

<style scoped>
.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
.task-list {
  background: #121212;
  padding: 20px;
  border-radius: 10px;
  max-width: 400px;
  color: #ccc;
}

.task-form {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.input,
.select,
.add-btn {
  padding: 8px;
  border: 1px solid #333;
  border-radius: 6px;
  background: #1e1e1e;
  color: #ccc;
}

.add-btn {
  background: #4caf50;
  color: #fff;
  cursor: pointer;
}

.add-btn:hover {
  background: #45a049;
}

.task-count {
  margin-bottom: 10px;
  color: #aaa;
}

.task-items {
  list-style: none;
  padding: 0;
}

.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
</style>
