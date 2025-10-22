<template>
  <teleport to="body">
    <div
      v-if="rendered"
      class="drawer-backdrop"
      ref="backdropElement"
      @click="handleBackdropClick"
      aria-hidden="true"
    ></div>

    <div
      v-if="rendered"
      ref="drawerElement"
      class="drawer"
      role="dialog"
      aria-modal="true"
      :aria-labelledby="titleId"
      tabindex="-1"
    >
      <div class="drawer-header">
        <button class="contact-btn font-sans" @click="close" aria-label="Close drawer">
          close
        </button>
        <div class="border">
          <svg
            width="12"
            height="12"
            viewBox="0 0 12 12"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
            class="left"
          >
            <path d="M6 0V12" stroke="currentColor" />
            <path d="M12 6L-3.57628e-07 6" stroke="currentColor" />
          </svg>
          <svg
            width="12"
            height="12"
            viewBox="0 0 12 12"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
            class="right"
          >
            <path d="M6 0V12" stroke="currentColor" />
            <path d="M12 6L-3.57628e-07 6" stroke="currentColor" />
          </svg>
        </div>
      </div>

      <div class="drawer-content">
        <slot></slot>
      </div>
    </div>
  </teleport>
</template>

<script setup lang="ts">
import gsap from 'gsap'
import { ref, watch, nextTick, onMounted, onUnmounted, computed } from 'vue'

defineOptions({
  name: 'AppDrawer',
})

interface Props {
  isOpen: boolean
}

const props = defineProps<Props>()

const emit = defineEmits<{
  close: []
}>()

const drawerElement = ref<HTMLElement>()
const backdropElement = ref<HTMLElement>()
const titleId = computed(() => `drawer-title-$${Math.random().toString(36).slice(2, 9)}`)
const previousActiveElement = ref<HTMLElement | null>(null)
const rendered = ref(false)

const close = () => {
  emit('close')
}

const handleBackdropClick = () => {
  close()
}

const handleEscapeKey = (event: KeyboardEvent) => {
  if (event.key === 'Escape' && props.isOpen) {
    close()
  }
}

const focusDrawer = async () => {
  await nextTick()
  if (drawerElement.value) {
    drawerElement.value.focus()
  }
}

const saveFocus = () => {
  previousActiveElement.value = document.activeElement as HTMLElement
}

const restoreFocus = () => {
  if (previousActiveElement.value && typeof previousActiveElement.value.focus === 'function') {
    previousActiveElement.value.focus()
  }
}

// Watch for open state changes
watch(
  () => props.isOpen,
  async (newValue) => {
    if (newValue) {
      // mount elements
      rendered.value = true
      saveFocus()
      await nextTick()
      focusDrawer()

      // animate backdrop and drawer in
      if (backdropElement.value) {
        gsap.fromTo(
          backdropElement.value,
          { opacity: 0 },
          { opacity: 0.8, duration: 0.25, ease: 'power1.out' },
        )
      }

      if (drawerElement.value) {
        gsap.fromTo(
          drawerElement.value,
          { x: '100%' },
          { x: '0%', duration: 0.4, ease: 'power3.out' },
        )
      }
    } else {
      // animate out then unmount
      const tl = gsap.timeline({
        onComplete: () => {
          rendered.value = false
          restoreFocus()
        },
      })

      if (backdropElement.value) {
        tl.to(backdropElement.value, { opacity: 0, duration: 0.2, ease: 'power1.in' }, 0)
      }

      if (drawerElement.value) {
        tl.to(drawerElement.value, { x: '100%', duration: 0.3, ease: 'power3.in' }, 0)
      }
    }
  },
)

onMounted(() => {
  document.addEventListener('keydown', handleEscapeKey)
})

onUnmounted(() => {
  document.removeEventListener('keydown', handleEscapeKey)
  restoreFocus()
})
</script>

<style scoped>
.drawer-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.8);
  opacity: 0;
  z-index: 999;
}
.drawer {
  position: fixed;
  padding: 24px;
  top: 0;
  right: 0;
  width: 100%;
  max-width: 600px;
  height: 100vh;
  background-color: #000000;
  z-index: 1000;
  display: flex;
  flex-direction: column;
  transform: none;
}

.drawer-header {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  padding: 4px;
  padding-bottom: 24px;
  position: relative;
}

.border {
  position: absolute;
  bottom: 0;
  left: 50%;
  height: 1px;
  background-color: #919191;
  color: #919191;
  width: calc(100% - 44px);
  transform: translateX(-50%);
  display: flex;
  gap: 8px;

  svg {
    position: absolute;
    bottom: -5.5px;

    &.left {
      left: -22px;
    }
    &.right {
      right: -22px;
    }
  }
}

.drawer-content {
  flex: 1;
  overflow-y: auto;
}

@media (max-width: 768px) {
  .drawer {
    max-width: 100%;
    width: 100vw;
  }
}
</style>
