<template>
  <div class="todo-container">
    <header>
      <h1>ToDo App</h1>
      <button @click="openModal">+ Add Task</button>
    </header>

    <div class="filters">
      <button @click="filter = 'all'" :class="{ active: filter === 'all' }">
        All
      </button>
      <button
        @click="filter = 'active'"
        :class="{ active: filter === 'active' }"
      >
        Active
      </button>
      <button
        @click="filter = 'completed'"
        :class="{ active: filter === 'completed' }"
      >
        Completed
      </button>
    </div>

    <ul class="task-list">
      <li
        v-for="task in filteredTasks"
        :key="task.id"
        :class="{ done: task.completed }"
      >
        <span @click="toggleTask(task.id)">{{ task.title }}</span>
        <div class="actions">
          <button @click="editTask(task)">✏️</button>
          <button @click="deleteTask(task.id)">🗑️</button>
        </div>
      </li>
    </ul>

    <!-- Modal 845857-->
    <div v-if="showModal" class="modal" @click.self="closeModal">
      <form class="modal-content">
        <h2>{{ isEditing ? "Update Task" : "Add New Task" }}</h2>
        <input v-model="taskInput" placeholder="Enter task..." />
        <div class="modal-buttons">
          <button @click="isEditing ? updateTask() : addTask()">
            {{ isEditing ? "Update" : "Add" }}
          </button>
          <button class="close-btn" @click="closeModal">Cancel</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

const tasks = ref([]);
const taskInput = ref("");
const filter = ref("all");
const showModal = ref(false);
const isEditing = ref(false);
const editingId = ref(null);

const openModal = () => {
  showModal.value = true;
};

const closeModal = () => {
  showModal.value = false;
  taskInput.value = "";
  isEditing.value = false;
};

const addTask = () => {
  if (!taskInput.value.trim()) return;
  tasks.value.push({
    id: Date.now(),
    title: taskInput.value.trim(),
    completed: false,
  });
  closeModal();
};

const editTask = (task) => {
  editingId.value = task.id;
  taskInput.value = task.title;
  isEditing.value = true;
  showModal.value = true;
};

const updateTask = () => {
  const task = tasks.value.find((t) => t.id === editingId.value);
  if (task && taskInput.value.trim()) {
    task.title = taskInput.value.trim();
    closeModal();
  }
};

const deleteTask = (id) => {
  tasks.value = tasks.value.filter((t) => t.id !== id);
};

const toggleTask = (id) => {
  const task = tasks.value.find((t) => t.id === id);
  if (task) task.completed = !task.completed;
};

const filteredTasks = computed(() => {
  if (filter.value === "active") {
    return tasks.value.filter((t) => !t.completed);
  } else if (filter.value === "completed") {
    return tasks.value.filter((t) => t.completed);
  }
  return tasks.value;
});
</script>


