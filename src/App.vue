<script lang="ts" setup>
import { ref } from 'vue'
import EntryEdit from './components/EntryEdit.vue'
import Header from './components/Header.vue'

const result = ref('')
const rows = ref([{ word: '', hint: '' }])
</script>

<template>
  <Header />
  <div class="body">
    <div class="editor">
      <div class="result-table">
        <p class="result-label">Result:</p>
        <input class="result" v-model="result" placeholder="Result" />
      </div>
      <EntryEdit v-model="rows" />
    </div>
  </div>
</template>

<style scoped>
.body {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.result-table {
  display: flex;
  flex-direction: row;
  gap: 10px;
}
.result-label {
  margin: 0;
  font: 1.4rem var(--font-family);
}
.editor {
  margin: 20px 40px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}
</style>
