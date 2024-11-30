<template>
    <li :class="taskClass" class="task-item">
      <transition name="fade">
        <div class="task-content">
          <input
            type="checkbox"
            :checked="task.completed"
            @change="toggleComplete"
          />
          <span
            v-if="!isEditing"
            :class="['task-title', priorityClass, { completed: task.completed }]"
            @dblclick="startEditing"
          >
            {{ task.title }}
          </span>
          <input
            v-else
            v-model="editedTitle"
            @blur="saveEdit"
            @keyup.enter="saveEdit"
            @keyup.esc="cancelEdit"
            class="edit-input"
          />
        </div>
      </transition>
      <button class="delete-btn" @click="deleteTask">✖</button>
    </li>
  </template>
  
  <script lang="ts">
  import { defineComponent, computed, ref } from "vue";
  import { Task } from "../interfaces/Task";
  
  export default defineComponent({
    name: "TaskItem",
    props: {
      task: {
        type: Object as () => Task,
        required: true,
      },
    },
    emits: ["delete-task", "toggle-complete", "update-title"],
    setup(props, { emit }) {
      const priorityClass = computed(() => {
        if (props.task.priority === "high") return "high-priority";
        if (props.task.priority === "medium") return "medium-priority";
        return "low-priority";
      });
  
      const toggleComplete = () => emit("toggle-complete", props.task.id);
      const deleteTask = () => emit("delete-task", props.task.id);
  
      const isEditing = ref(false);
      const editedTitle = ref(props.task.title);
  
      const startEditing = () => {
        isEditing.value = true;
        editedTitle.value = props.task.title;
      };
  
      const saveEdit = () => {
        if (editedTitle.value.trim()) {
          emit("update-title", { id: props.task.id, title: editedTitle.value });
          isEditing.value = false;
        } else {
          cancelEdit();
        }
      };
  
      const cancelEdit = () => {
        editedTitle.value = props.task.title;
        isEditing.value = false;
      };
  
      return {
        priorityClass,
        toggleComplete,
        deleteTask,
        isEditing,
        editedTitle,
        startEditing,
        saveEdit,
        cancelEdit,
      };
    },
  });
  </script>

<style scoped>
 .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.5s ease;
  }
  .fade-enter-from,
  .fade-leave-to {
    opacity: 0;
  }
.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  background: #1e1e1e;
  border: 1px solid #333;
  border-radius: 6px;
  margin-bottom: 10px;
  transition: transform 0.2s;
}

.edit-input {
  padding: 5px;
  font-size: 1em;
  background: #1e1e1e;
  color: #ccc;
  border: 1px solid #333;
  border-radius: 4px;
  width: 100%;
}

.task-item:hover {
  transform: scale(1.02);
}

.task-content {
  display: flex;
  align-items: center;
  gap: 10px;
}

.task-title {
  color: #ccc;
  transition: color 0.3s ease, text-decoration 0.3s ease;
}

/* Completed tasks */
.completed {
  text-decoration: line-through;
  color: #555;
}

.high-priority {
  color: #e74c3c; /* Red for high priority */
}

.medium-priority {
  color: #f1c40f; /* Yellow for medium priority */
}

.low-priority {
  color: #2ecc71; /* Green for low priority */
}


.delete-btn {
  background: none;
  border: none;
  color: #e74c3c;
  cursor: pointer;
}

.delete-btn:hover {
  color: #ff6f61;
}
</style>
