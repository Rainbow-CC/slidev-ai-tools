<script setup lang="ts">
import { ref, computed } from 'vue'

const props = defineProps<{
  src: string
  alt?: string
}>()

const isOpen = ref(false)
const scale = ref(1)
const position = ref({ x: 0, y: 0 })
const isDragging = ref(false)
const startPos = ref({ x: 0, y: 0 })
// Used to distinguish between drag and click
const dragDistance = ref(0)

function openModal() {
  isOpen.value = true
  scale.value = 1
  position.value = { x: 0, y: 0 }
  document.body.style.overflow = 'hidden' // Prevent background scrolling
}

function closeModal() {
  isOpen.value = false
  document.body.style.overflow = ''
}

function handleWheel(e: WheelEvent) {
  e.preventDefault()
  const delta = e.deltaY > 0 ? -0.2 : 0.2
  const newScale = scale.value + delta
  scale.value = Math.max(0.5, Math.min(8, newScale)) // Increased max zoom
}

function toggleZoom(e: MouseEvent) {
  if (dragDistance.value > 5) return // It was a drag, not a click

  if (scale.value > 1.2) {
    // Reset to fit
    scale.value = 1
    position.value = { x: 0, y: 0 }
  } else {
    // Zoom in
    scale.value = 2.5
    // Optional: could calculate offset to zoom into mouse position, but center is safer for now
  }
}

function startDrag(e: MouseEvent) {
  // Allow initiating drag logic, but only move if scaled > 1 (handled in onDrag)
  isDragging.value = true
  dragDistance.value = 0
  startPos.value = { x: e.clientX - position.value.x, y: e.clientY - position.value.y }
  e.preventDefault()
}

function onDrag(e: MouseEvent) {
  if (!isDragging.value) return
  
  // Calculate distance moved to distinguish click from drag
  const dx = e.clientX - startPos.value.x - position.value.x
  const dy = e.clientY - startPos.value.y - position.value.y
  dragDistance.value += Math.abs(dx) + Math.abs(dy)

  if (scale.value <= 1) return // Don't pan if not zoomed

  position.value = {
    x: e.clientX - startPos.value.x,
    y: e.clientY - startPos.value.y
  }
}

function stopDrag() {
  isDragging.value = false
}

const imageStyle = computed(() => ({
  transform: `translate(${position.value.x}px, ${position.value.y}px) scale(${scale.value})`,
  cursor: scale.value > 1 ? (isDragging.value ? 'grabbing' : 'grab') : 'zoom-in',
  transition: isDragging.value ? 'none' : 'transform 0.3s cubic-bezier(0.25, 0.8, 0.25, 1)'
}))
</script>

<template>
  <div class="image-modal-container inline-block w-full">
    <!-- Trigger Image -->
    <div 
      class="group relative cursor-pointer overflow-hidden rounded-lg shadow-xl border border-white/10 bg-black/20" 
      @click="openModal"
    >
      <img 
        :src="src" 
        :alt="alt"
        class="w-full h-auto object-cover transition-all duration-500 group-hover:scale-105 group-hover:brightness-110"
      />
      <!-- Hover Overlay -->
      <div class="absolute inset-0 flex items-center justify-center opacity-0 group-hover:opacity-100 transition-opacity duration-300 bg-black/30">
        <div class="bg-black/60 text-white px-4 py-2 rounded-full flex items-center gap-2 backdrop-blur-sm transform translate-y-4 group-hover:translate-y-0 transition-transform">
          <carbon:zoom-in class="text-xl" />
          <span class="text-sm font-medium">点击放大</span>
        </div>
      </div>
    </div>

    <!-- Modal Overlay -->
    <transition
      enter-active-class="transition duration-300 ease-out"
      enter-from-class="opacity-0"
      enter-to-class="opacity-100"
      leave-active-class="transition duration-200 ease-in"
      leave-from-class="opacity-100"
      leave-to-class="opacity-0"
    >
      <div 
        v-if="isOpen" 
        class="fixed inset-0 z-[999] flex items-center justify-center bg-black/95 backdrop-blur-sm"
        @click.self="closeModal"
        @mousemove="onDrag"
        @mouseup="stopDrag"
        @mouseleave="stopDrag"
      >
        <!-- Toolbar -->
        <div class="absolute top-6 right-6 flex items-center gap-4 z-[1000]">
          <div class="bg-white/10 backdrop-blur rounded-full px-4 py-1.5 text-white/80 text-sm border border-white/10 hidden sm:block select-none">
            {{ Math.round(scale * 100) }}%
          </div>
          
          <button 
            class="p-2.5 rounded-full bg-white/10 hover:bg-white/20 text-white transition-colors border border-white/10 backdrop-blur group"
            title="Reset Zoom"
            @click="scale = 1; position = { x: 0, y: 0 }"
          >
            <carbon:fit-to-screen class="text-xl group-active:scale-90 transition-transform" />
          </button>
          
          <button 
            class="p-2.5 rounded-full bg-white/10 hover:bg-red-500/80 text-white transition-colors border border-white/10 backdrop-blur group"
            title="Close (Esc)"
            @click="closeModal"
          >
            <carbon:close class="text-xl group-active:scale-90 transition-transform" />
          </button>
        </div>

        <!-- Main Image Area -->
        <div 
          class="w-full h-full flex items-center justify-center overflow-hidden p-4 sm:p-10"
          @wheel="handleWheel"
        >
          <img 
            :src="src" 
            class="max-w-full max-h-full object-contain select-none shadow-2xl"
            :style="imageStyle"
            @mousedown="startDrag"
            @click.stop="toggleZoom"
            draggable="false"
          />
        </div>
        
        <!-- Bottom Hint -->
        <div class="absolute bottom-6 left-1/2 -translate-x-1/2 pointer-events-none transition-opacity duration-300" :class="scale > 1 ? 'opacity-0' : 'opacity-100'">
          <div class="bg-black/60 backdrop-blur border border-white/10 px-4 py-2 rounded-full text-white/70 text-xs sm:text-sm flex items-center gap-2 shadow-lg">
            <carbon:cursor-1 class="text-base" />
            <span>点击或滚动缩放</span>
            <span class="w-1 h-1 bg-white/30 rounded-full mx-1"></span>
            <carbon:move class="text-base" />
            <span>拖动查看细节</span>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>