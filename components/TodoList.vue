<template>
  <UCard class="todo-list-card">
    <template #header>{{ status }}</template>
    <div class="buttons">
      <UButton
        :disabled="selectedTodos.length === 0"
        @click="deleteTodos" v-if="['incomplete'].indexOf(status) > -1"
      >Delete</UButton>
      <UButton
        :disabled="selectedTodos.length === 0"
        @click="updateTodos('complete')"
        v-if="['incomplete', 'archived'].indexOf(status) > -1"
      >Complete</UButton>
      <UButton
        :disabled="selectedTodos.length === 0"
        @click="updateTodos('incomplete')"
        v-if="['complete', 'archived'].indexOf(status) > -1"
      >Incomplete</UButton>
      <UButton
        :disabled="selectedTodos.length === 0" 
        @click="updateTodos('archived')"
        v-if="['incomplete', 'complete'].indexOf(status) > -1"
      >Archive</UButton>
    </div>
    <UTable
      show-header
      :rows="todos"
      v-model="selectedTodos"
      :columns="[{key:'created_at',label:'Created'},{key:'name',label:'Name'}]"
    >
      <template #created_at-data="{ row }">
        {{ useFormatDateTimeString(row.created_at) }}
      </template>
    </UTable>
  </UCard>
</template>

<style scoped>
.todo-list-card {
  display: flex;
  flex-direction: column;
}
</style>

<script lang="ts" setup>
  // there is probably a better place for this - generated from cli?
  export type TodoStatus = 'complete' | 'incomplete' | 'archived'
  export interface Props {
    status: TodoStatus,
    todos: []
  }
  const props = defineProps<Props>()

  const emit = defineEmits<{
    (e: 'updateTodos', todoIds: string[], status: TodoStatus): void
    (e: 'deleteTodos', todoIds: string[]): void
  }>()

  // get rid of this any
  const selectedTodos: Ref<any[]> = ref([])

  const updateTodos = async (status: TodoStatus) => {
    const todoIds = selectedTodos.value.map(td => td.id)
    emit('updateTodos', todoIds , status)  
  }
  const deleteTodos = async () => {
    const todoIds = selectedTodos.value.map(td => td.id)
    emit('deleteTodos', todoIds)
  }

  watch(() => props.todos, () => {
    selectedTodos.value = []
  })
</script>