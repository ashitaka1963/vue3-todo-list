<script setup lang="ts">
import { ref, computed } from 'vue';

interface Task {
  title: string;
  createDate: Date;
}

interface SortItem {
  title: string;
  key: string;
}

const sortItems: Array<SortItem> = [
  { title: 'あいうえお順', key: 'title' },
  { title: '作成日', key: 'createDate' }
];

const getSessionStorage = (key: string): Task[] => {
  const getItem = sessionStorage.getItem(key);
  return getItem !== null ? JSON.parse(getItem) : [];
};

let taskListInit: Task[] = getSessionStorage('todoTaskList');
let doneListInit: Task[] = getSessionStorage('doneTaskList');

const selectSort = ref({ title: 'あいうえお順', key: 'title' });
const newTaskName = ref('');
const taskList = ref(taskListInit);
const doneList = ref(doneListInit);

const sortedTaskList = computed((): Task[] => {
  const sortKey: string = selectSort.value.key;
  let result = [...taskList.value].sort((a: any, b: any) => (a[sortKey] < b[sortKey] ? -1 : 1));
  return result;
});

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

  setTaskListToSessionStorage();
};

// タスク移動(Todo⇒Done)
const moveDoneTask = (index: number): void => {
  doneList.value.push(taskList.value[index]);
  taskList.value.splice(index, 1);

  setTaskListToSessionStorage();
};

// タスク移動(Done⇒Todo)
const moveTodoTask = (index: number): void => {
  taskList.value.push(doneList.value[index]);
  doneList.value.splice(index, 1);

  setTaskListToSessionStorage();
};

// タスク削除
const deleteTask = (index: number): void => {
  taskList.value.splice(index, 1);

  setTaskListToSessionStorage();
};

// セッション保存
const setTaskListToSessionStorage = (): void => {
  sessionStorage.setItem('todoTaskList', JSON.stringify(taskList.value));
  sessionStorage.setItem('doneTaskList', JSON.stringify(doneList.value));
};
</script>

<template>
  <v-container>
    <v-row no-gutters justify="center">
      <v-col cols="5">
        <div :class="['text-h6', 'pa-2']">今日の予定</div>
      </v-col>
      <v-col cols="3">
        <v-select
          v-model="selectSort"
          :items="sortItems"
          item-title="title"
          item-value="key"
          variant="underlined"
          return-object
          prepend-inner-icon="mdi-sort"
        ></v-select>
      </v-col>
    </v-row>
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
          <v-list-item
            v-for="(task, index) in sortedTaskList"
            :key="task.title"
            :title="task.title"
          >
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
