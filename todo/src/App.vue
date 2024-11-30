<template>
  <div id="app">
    <h1 class="title">To-Do List</h1>
    <TaskList />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import TaskList from "./components/TaskList.vue";

export default defineComponent({
  name: "App",
  components: { TaskList },
});
</script>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.title{
  margin-top: 100px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin: 0;
  padding: 0;
  width: 100%;
  min-height: 100vh;
  background-color: #121212; 
  color: #ffffff; 
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  box-sizing: border-box;
}
</style>
