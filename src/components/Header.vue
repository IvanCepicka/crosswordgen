<template>
  <div class="header">
    <img src="/icon.svg" alt="crossword icon" class="icon" draggable="false" />
    <p class="title">Crossword Generator</p>
  </div>
</template>

<style scoped>
.header {
  background-color: var(--theme-color);
  color: white;
  font-family: var(--font-family);
  display: flex;
  flex-direction: row;
  gap: 12px;
  padding: 8px 15px;
  align-items: center;
  user-select: none;
}
.header p {
  font-size: 1.75rem;
  margin: 0;
}
.icon {
  height: 45px;
}
</style>
