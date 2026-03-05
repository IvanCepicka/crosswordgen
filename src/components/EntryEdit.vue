<template>
  <div class="entry-edit">
    <div class="table">
      <div class="header">Word:</div>
      <div class="header">Hint:</div>
      <div></div>

      <template v-for="(row, index) in rows" :key="index">
        <input class="word" v-model="row.word" placeholder="Word" />
        <input class="hint" v-model="row.hint" placeholder="Hint" />
        <button v-if="rows.length > 1" @click="removeRow(index)">Remove</button>
      </template>
    </div>

    <div class="button-row">
      <button @click="addRow()">Add Word</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'

type Row = {
  word: string
  hint: string
}

const props = defineProps<{
  modelValue: Row[]
}>()

const emit = defineEmits<{
  (e: 'update:modelValue', value: Row[]): void
}>()

const rows = ref<Row[]>([...props.modelValue])

watch(
  rows,
  (val) => {
    emit('update:modelValue', val)
  },
  { deep: true },
)

function addRow() {
  rows.value.push({ word: '', hint: '' })
}

function removeRow(index: number) {
  rows.value.splice(index, 1)
}
</script>

<style scoped>
.entry-edit {
  display: flex;
  flex-direction: column;
  gap: 5px;
}
.header {
  font: 1.3rem var(--font-family);
  font-weight: 400;
}
.table {
  display: grid;
  grid-template-columns: 200px 500px 100px;
  gap: 5px;
}
.button-row {
  display: flex;
  flex-direction: row;
  justify-content: center;
}
</style>
