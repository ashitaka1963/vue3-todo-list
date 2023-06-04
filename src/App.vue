<script setup lang="ts">
import TodoList from './components/TodoList.vue';
</script>

<template>
  <v-app>
    <v-app-bar color="primary">
      <template v-slot:prepend>
        <v-icon icon="mdi-checkbox-marked-circle-plus-outline"></v-icon>
      </template>
      <v-app-bar-title>ToDo</v-app-bar-title>
    </v-app-bar>
    <v-main>
      <TodoList />
    </v-main>
  </v-app>

  <!-- </main> -->
</template>

<style scoped></style>
