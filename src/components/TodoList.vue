<script setup lang="ts">
// TODO:ソート
// TODO:データ保存

import { ref } from 'vue';

interface Task {
  title: string;
  createDate: Date;
}

const taskListInit: Task[] = [];
const doneListInit: Task[] = [];

const newTaskName = ref('');
const taskList = ref(taskListInit);
const doneList = ref(doneListInit);

// タスク追加
const insertTask = (): void => {
  if (newTaskName.value == '') {
    return;
  }

  const tempTask: Task = {
    title: newTaskName.value,
    createDate: new Date()
  };

  taskList.value.push(tempTask);
  newTaskName.value = '';
};

// タスク移動(Todo⇒Done)
const moveDoneTask = (index: number): void => {
  doneList.value.push(taskList.value[index]);
  taskList.value.splice(index, 1);
};

// タスク移動(Done⇒Todo)
const moveTodoTask = (index: number): void => {
  taskList.value.push(doneList.value[index]);
  doneList.value.splice(index, 1);
};

// タスク削除
const deleteTask = (index: number): void => {
  taskList.value.splice(index, 1);
};
</script>

<template>
  <v-container>
    <v-row no-gutters justify="center">
      <v-col cols="8">
        <v-text-field
          v-model="newTaskName"
          label="タスクの追加"
          variant="solo"
          prepend-inner-icon="mdi-plus"
          single-line
          v-on:keydown.enter="insertTask"
        >
        </v-text-field>
      </v-col>
    </v-row>

    <!-- TodoList -->
    <v-row no-gutters justify="center">
      <v-col cols="8">
        <v-list>
          <v-list-item v-for="(task, index) in taskList" :key="task.title" :title="task.title">
            <template v-slot:prepend>
              <v-list-item-action start>
                <v-radio color="indigo" v-on:click="moveDoneTask(index)"></v-radio>
              </v-list-item-action>
            </template>
            <template v-slot:append>
              <v-icon
                color="indigo"
                icon="mdi-close"
                size="small"
                v-on:click="deleteTask(index)"
              ></v-icon>
            </template>
          </v-list-item>
        </v-list>
      </v-col>
    </v-row>

    <!-- DoneList -->
    <v-row no-gutters justify="center">
      <v-col cols="8">
        <v-expansion-panels v-if="doneList.length > 0">
          <v-expansion-panel>
            <v-expansion-panel-title> 完了済み {{ doneList.length }} </v-expansion-panel-title>
            <v-expansion-panel-text>
              <v-list>
                <v-list-item
                  v-for="(task, index) in doneList"
                  :key="task.title"
                  :title="task.title"
                >
                  <template v-slot:prepend>
                    <v-list-item-action start>
                      <v-radio
                        color="indigo"
                        :model-value="true"
                        v-on:click="moveTodoTask(index)"
                      ></v-radio>
                    </v-list-item-action>
                  </template>
                </v-list-item>
              </v-list>
            </v-expansion-panel-text>
          </v-expansion-panel>
        </v-expansion-panels>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped></style>
