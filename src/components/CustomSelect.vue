<template>
  <div class="custom-select" :class="{ open: isOpen }" ref="rootElement">
    <label v-if="label" :for="selectId" class="select-label font-mono">
      {{ label }}<span v-if="required" class="required">*</span>
    </label>

    <div
      class="select-trigger"
      :class="{ selected: !isValueEmpty }"
      :id="selectId"
      role="combobox"
      :aria-expanded="isOpen"
      :aria-activedescendant="selectedOptionId"
      :aria-controls="listboxId"
      aria-autocomplete="list"
      tabindex="0"
      @click="toggleDropdown"
      @keydown="handleTriggerKeydown"
      ref="triggerElement"
    >
      <span class="select-value font-sans" :class="{ placeholder: isValueEmpty }">{{
        selectedLabel
      }}</span>
      <svg
        width="16"
        height="14"
        viewBox="0 0 16 14"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
        class="select-arrow"
      >
        <path d="M7.79419 0L15.5884 13.5L-3.91006e-05 13.5L7.79419 0Z" fill="currentColor" />
      </svg>
    </div>

    <ul
      v-if="isOpen"
      :id="listboxId"
      class="select-options"
      role="listbox"
      :aria-labelledby="selectId"
      ref="optionsElement"
    >
      <li
        v-for="(option, index) in options"
        :key="option.value"
        class="select-option font-sans"
        :class="{
          'is-highlighted': index === highlightedIndex,
        }"
        role="option"
        :aria-selected="option.value === modelValue"
        :id="`${selectId}-option-${index}`"
        @click="selectOption(option)"
        @mouseenter="highlightedIndex = index"
        @mouseleave="highlightedIndex = -1"
      >
        {{ option.label }}
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import gsap from 'gsap'
import { ref, computed, watch, nextTick, onMounted, onUnmounted } from 'vue'

interface Option {
  value: string | number
  label: string
}

interface Props {
  modelValue: string | number | null
  options: Option[]
  label?: string
  placeholder?: string
  required?: boolean
}

const props = defineProps<Props>()

const emit = defineEmits<{
  'update:modelValue': [value: string | number | null]
}>()

const isOpen = ref(false)
const highlightedIndex = ref(-1)
const triggerElement = ref<HTMLElement | null>(null)
const optionsElement = ref<HTMLElement | null>(null)
const rootElement = ref<HTMLElement | null>(null)

const selectId = computed(() => `select-${Math.random().toString(36).slice(2, 9)}`)
const selectedLabel = computed(() => {
  const selected = props.options.find((option) => option.value === props.modelValue)
  return selected ? selected.label : props.placeholder
})

const listboxId = computed(() => `${selectId.value}-listbox`)

// aria-activedescendant should point to the currently highlighted option when open,
// otherwise fall back to the selected option (based on modelValue)
const selectedOptionId = computed(() => {
  const highlighted = highlightedIndex.value
  if (highlighted >= 0) return `${selectId.value}-option-${highlighted}`
  if (props.modelValue === null) return undefined
  const index = props.options.findIndex((option) => option.value === props.modelValue)
  return index >= 0 ? `${selectId.value}-option-${index}` : undefined
})

const isValueEmpty = computed(() => {
  return props.modelValue === null || props.modelValue === undefined
})

const closeDropdown = () => {
  if (isOpen.value && optionsElement.value) {
    // ensure we don't have multiple competing tweens
    try {
      gsap.killTweensOf(optionsElement.value)
    } catch {
      /* ignore if killTweensOf fails for any reason */
    }
    gsap.to(optionsElement.value, {
      opacity: 0,
      y: -10,
      duration: 0.2,
      ease: 'power2.in',
      onComplete: () => {
        console.log('close')
        isOpen.value = false
        highlightedIndex.value = -1
      },
    })
    // if optionsElement is missing, ensure dropdown closes
  } else {
    isOpen.value = false
    highlightedIndex.value = -1
  }
}

const toggleDropdown = () => {
  if (isOpen.value) {
    // if it's open, animate close consistently
    closeDropdown()
    return
  }

  // otherwise open
  isOpen.value = true
  const idx = props.options.findIndex((option) => option.value === props.modelValue)
  highlightedIndex.value = idx >= 0 ? idx : props.options.length > 0 ? 0 : -1
  nextTick(() => {
    if (optionsElement.value) {
      gsap.fromTo(
        optionsElement.value,
        {
          opacity: 0,
          y: -10,
        },
        {
          opacity: 1,
          y: 0,
          duration: 0.2,
          ease: 'power2.out',
        },
      )
    }
  })
}

const selectOption = (option: Option) => {
  emit('update:modelValue', option.value)
  // animate close so selection follows the same behavior as clicking outside
  closeDropdown()
}

const handleTriggerKeydown = (event: KeyboardEvent) => {
  switch (event.key) {
    case 'Enter':
    case ' ':
      event.preventDefault()
      if (isOpen.value) {
        if (highlightedIndex.value >= 0 && highlightedIndex.value < props.options.length) {
          const selectedOption = props.options[highlightedIndex.value]
          if (selectedOption) {
            selectOption(selectedOption)
          }
        }
      } else {
        toggleDropdown()
      }
      break
    case 'ArrowDown':
      event.preventDefault()
      if (!isOpen.value) {
        toggleDropdown()
      } else {
        highlightedIndex.value = Math.min(highlightedIndex.value + 1, props.options.length - 1)
        scrollToHighlighted()
      }
      break
    case 'ArrowUp':
      event.preventDefault()
      if (!isOpen.value) {
        toggleDropdown()
      } else {
        highlightedIndex.value = Math.max(highlightedIndex.value - 1, 0)
        scrollToHighlighted()
      }
      break
    case 'Escape':
      event.preventDefault()
      closeDropdown()
      break
    case 'Tab':
      // allow default tab behavior but close the dropdown
      closeDropdown()
      break
  }
}

const scrollToHighlighted = () => {
  if (highlightedIndex.value >= 0 && optionsElement.value) {
    const highlightedOption = optionsElement.value.children[highlightedIndex.value] as HTMLElement
    if (highlightedOption) {
      highlightedOption.scrollIntoView({ block: 'nearest' })
    }
  }
}

const handleClickOutside = (event: MouseEvent) => {
  const target = event.target as Node
  if (rootElement.value && !rootElement.value.contains(target)) {
    closeDropdown()
  }
}

// Watch for model value changes to update highlighted index
watch(
  () => props.modelValue,
  (newValue) => {
    if (newValue === null) {
      highlightedIndex.value = -1
      return
    }
    highlightedIndex.value = props.options.findIndex((option) => option.value === newValue)
  },
)

onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})
</script>

<style scoped>
.custom-select {
  position: relative;
  width: 100%;
}

.select-label {
  margin-bottom: 8px;
  display: block;
  font-weight: 400;
  font-size: 12px;
  color: #909090;
  text-transform: uppercase;
  line-height: 125%;
}

.select-trigger {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  padding: 16px;
  border: 1px solid #fffef4;
  border-radius: 8px;
  cursor: pointer;
  transition: border-color 0.2s ease-in-out;
  outline: none;
}

.custom-select:not(.open) .select-trigger.selected {
  border-color: #00ff6a;
}

.open {
  & .select-trigger {
    border-bottom: none;
    border-bottom-left-radius: 0;
    border-bottom-right-radius: 0;

    transition:
      border-radius 0.1s ease-in-out,
      border-color 0.2s ease-in-out;
  }
}

.select-value {
  flex: 1;
  color: #fffef4;
  font-size: 16px;
  font-weight: 700;

  &.placeholder {
    color: #909090; /* dimmed placeholder color */
  }
}

.select-arrow {
  color: #fffef4;
  transition: transform 0.2s ease;
  transform: rotate(-180deg);
}

.open {
  & .select-arrow {
    transform: rotate(0deg);
  }
}

.select-options {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  z-index: 50;
  border: 1px solid #fffef4;
  border-top: none;
  background: #000000;
  list-style: none;
  padding: 0;
  border-radius: 8px;
  border-top-left-radius: 0;
  border-top-right-radius: 0;
}

.select-option {
  cursor: pointer;
  color: #fffef4;
  padding-block: 6px;
  padding-inline: 16px;
  background-color: transparent;
  transition:
    background-color 0.1s ease,
    color 0.1s ease;
  font-weight: 700;
  font-size: 16px;
  line-height: 110%;
  letter-spacing: 1%;
  text-transform: uppercase;
}

.select-option:hover,
.select-option.is-highlighted {
  &:hover,
  &.is-highlighted {
    background-color: #fffef4;
    color: #000000;
  }
}
</style>
