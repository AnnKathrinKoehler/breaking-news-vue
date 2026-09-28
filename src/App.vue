<script setup lang="ts">
import { onMounted, onUnmounted, ref } from 'vue';

interface NewsItem {
  id: number
  title: string
  link: string
}
const breakingNews: NewsItem[] = [
  {
    id: 1,
    title: "Ducks sighted!",
    link: "https://en.wikipedia.org/wiki/Duck#/media/File:Bucephala-albeola-010.jpg",
  },
  {
    id: 2,
    title: "Moon might contain cheese!",
    link: "https://en.wikipedia.org/wiki/The_Moon_is_made_of_green_cheese",
  },
];


// Close 

const count = breakingNews.length;

const wrapper = ref<HTMLElement | null>(null)
const visible = ref(false);

function showModal() {
  if (didDrag) {
    didDrag = false
    return
  }
  if (count === 0 ) {
    visible.value = false
  }
  visible.value = !visible.value
}

const handleClose = (event: KeyboardEvent) => {
  if (event.key === "Escape") {
    visible.value = false
  }
};

const handleOutsideClick = (event: MouseEvent) => {
  const target = event.target as Node

  if (wrapper.value && !wrapper.value.contains(target)) {
    visible.value = false
  }
}

onMounted(() => {
  document.addEventListener('keyup', handleClose)
  document.addEventListener('click', handleOutsideClick)
});

onUnmounted(() => {
  document.removeEventListener('keyup', handleClose)
  document.removeEventListener('click', handleOutsideClick)
});

// Dragging

const x = ref(24)
const y = ref(24)
const isDragging = ref(false)

let offsetX = 0
let offsetY = 0
let didDrag = false

function startDrag(event: PointerEvent): void {
  const button = event.currentTarget as HTMLElement
  const rect = button.getBoundingClientRect()

  offsetX = event.clientX - rect.left
  offsetY = event.clientY - rect.top

  didDrag = false
  isDragging.value = true

  button.setPointerCapture(event.pointerId)
}

function moveBubble(event: PointerEvent): void {
  if (!isDragging.value) return

  didDrag = true

  x.value = event.clientX - offsetX
  y.value = event.clientY - offsetY
}

function stopDrag(): void {
  isDragging.value = false
}

</script>

<template>
  <div
  ref="wrapper"
  class="news-bubble--wrapper"
  :style="{ left: `${x}px`, top: `${y}px` }"
>
  <button
    type="button"
    aria-label="Öffne News-Menü oder ziehe den Button zum Verschieben"
    @click="showModal"
    @pointerdown="startDrag"
    @pointermove="moveBubble"
    @pointerup="stopDrag"
    @pointercancel="stopDrag"
  >
    {{ count }}
  </button>

  <ul v-if="visible" class="news-menu" aria-label="News-Menü">
    <li v-for="item in breakingNews" :key="item.id">
      <a :href="item.link" target="_blank">{{ item.title }}</a>
    </li>
  </ul>
</div>
</template>

<style>
  .news-bubble--wrapper {
    position: fixed;
    touch-action: none;
    cursor: grab;
  }

  .news-menu {
    position: absolute;
    right: -6px;
    background-color: #292929;
    color: white;
    list-style: none;
    top: 0;
    transform: translateX(100%);
    width: 260px;
  }

  li > a {
    color: white;
    padding: 0;
  }

  ul {
    padding: 20px;
  }

  ul li:not(:last-child) {
    margin-bottom: 10px;
    border-bottom: 1px solid white;
    padding-bottom: 10px;
  }

  li a:focus, li a:hover {
    background-color: blue;
  }

  button {
    border-radius: 50%;
    background-color: #292929;
    color: white;
    padding: 5px;
    font-size: 18px;
    text-align: cener;
    width: 50px;
    height: 50px;
    font-weight: 700;
  }

  button:focus {
    outline-color: blue;
    border: 5px solid #7f7fec;
  }
</style>
