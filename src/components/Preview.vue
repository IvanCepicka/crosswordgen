<template>
  <canvas ref="canvasRef" :width="canvasSize" :height="canvasSize"></canvas>
</template>

<script setup lang="ts">
import { ref, watch, onMounted } from 'vue'
// @ts-ignore
import { generateLayout } from 'crossword-layout-generator'

type Row = { word: string; hint: string }

const props = defineProps<{ words: Row[]; solved?: boolean }>()

const canvasRef = ref<HTMLCanvasElement | null>(null)
const canvasSize = 600
const gridPadding = 20
let layout: any = null

function genLayout(words: Row[]) {
  const filtered = words.filter((w) => w.word?.trim() && w.hint?.trim())
  if (filtered.length === 0) return null

  const input = filtered.map((w) => ({
    answer: w.word.trim().toUpperCase(),
    clue: w.hint.trim(),
  }))

  return generateLayout(input)
}

function computeCanvasHeight(): number {
  if (!layout || !layout.table || !layout.table[0]) return 600

  const rows = layout.table.length
  const cellSizeX = (canvasSize - gridPadding * 2) / layout.table[0].length
  const cellSizeY = (canvasSize - gridPadding * 2) / rows
  const cellSize = Math.min(cellSizeX, cellSizeY)

  const clueLines = layout.result ? layout.result.length : 0
  const clueHeight = clueLines * (cellSize * 0.4) + 40

  return gridPadding * 2 + rows * cellSize + clueHeight
}

function drawLayout() {
  if (!canvasRef.value || !layout || !layout.table || !layout.table[0]) return
  canvasRef.value.height = computeCanvasHeight()

  const ctx = canvasRef.value.getContext('2d')
  if (!ctx) return

  ctx.clearRect(0, 0, canvasSize, canvasSize)

  const rows = layout.table.length
  const cols = layout.table[0].length
  const cellSizeX = (canvasSize - gridPadding * 2) / cols
  const cellSizeY = (canvasSize - gridPadding * 2) / rows
  const cellSize = Math.min(cellSizeX, cellSizeY)

  ctx.font = `${cellSize * 0.6}px Arial`
  ctx.textAlign = 'center'
  ctx.textBaseline = 'middle'
  ctx.fillStyle = 'black'
  ctx.strokeStyle = 'black'

  layout.table.forEach((row: any[], y: number) => {
    row.forEach((letter: any, x: number) => {
      if (!letter || letter === '-') return

      const px = gridPadding + x * cellSize
      const py = gridPadding + y * cellSize

      ctx.fillStyle = 'white'
      ctx.fillRect(px, py, cellSize, cellSize)

      ctx.strokeStyle = 'black'
      ctx.strokeRect(px, py, cellSize, cellSize)

      if (props.solved) {
        ctx.fillStyle = 'black'
        ctx.fillText(letter.toUpperCase(), px + cellSize / 2, py + cellSize / 2)
      }
    })
  })

  const columns = 3
  const clueMargin = 10
  const clueFontSize = cellSize * 0.3

  const clues = layout.result.filter((r: any) => r.position)
  const perCol = Math.ceil(clues.length / columns)

  ctx.font = `${clueFontSize}px Arial`
  ctx.textAlign = 'left'
  ctx.textBaseline = 'top'
  ctx.fillStyle = 'black'

  for (let col = 0; col < columns; col++) {
    const clueX =
      gridPadding +
      col * ((canvasSize - gridPadding * 2) / columns) +
      clueMargin
    let clueY = gridPadding + rows * cellSize + 20

    for (let i = 0; i < perCol; i++) {
      const idx = col * perCol + i
      if (idx >= clues.length) break

      const entry = clues[idx]
      ctx.fillText(`${entry.position}. ${entry.clue}`, clueX, clueY)
      clueY += clueFontSize * 1.2
    }
  }

  layout.result
    .filter((r: any) => r.position)
    .forEach((entry: any) => {
      const { startx, starty, position } = entry

      const px = gridPadding + (startx - 1) * cellSize
      const py = gridPadding + (starty - 1) * cellSize

      ctx.font = `${cellSize * 0.3}px Arial`
      ctx.textAlign = 'left'
      ctx.textBaseline = 'top'
      ctx.fillStyle = 'black'

      ctx.fillText(`${position}`, px + 2, py + 2)
    })
}

onMounted(() => {
  layout = genLayout(props.words)
  drawLayout()
})

watch(
  () => props.words,
  (newWords) => {
    const filtered = newWords
      .filter((w) => w.word?.trim() && w.hint?.trim())
      .map((w) => ({
        answer: w.word.trim().toUpperCase(),
        clue: w.hint.trim(),
      }))

    if (filtered.length === 0) return

    layout = generateLayout(filtered)
    drawLayout()
  },
  { deep: true, immediate: true },
)
</script>
