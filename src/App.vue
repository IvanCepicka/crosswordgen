<script lang="ts" setup>
import { ref } from 'vue'
import EntryEdit from './components/EntryEdit.vue'
import Header from './components/Header.vue'
import Preview from './components/Preview.vue'

const rows = ref([{ word: '', hint: '' }])
</script>

<template>
  <Header />
  <div class="body">
    <div class="editor">
      <EntryEdit v-model="rows" />
    </div>
    <div class="preview">
      <Preview :words="rows" :solved="true" />
      <Preview :words="rows" :solved="false" />
    </div>
  </div>
</template>

<style scoped>
.body {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.editor {
  margin: 20px 40px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.preview {
  width: 600px;
  margin: 10px;
}
</style>
