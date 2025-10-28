<template>
  <div class="flex">
    <div 
        class="rounded-full w-[88px] h-[88px] mr-[16px] bg-gray-200 bg-cover bg-center transition-all"
        :class="isFocused ? 'avatar-focused' : ''"
        :style="{ backgroundImage: thumb ? `url(${thumb})` : 'none' }"
      >
    </div>
    
      <label 
        class="tracking-wide transition-colors flex flex-col justify-between py-[5px]"
      >
        <p class="uppercase text-[16px] font-[Koulen]" :class="isFocused ? 'text-primary' : 'text-black'">{{ label }}</p>
        <div class="flex items-center relative">
          <span 
            ref="measureRef" 
            class="absolute invisible whitespace-pre font-medium"
          >
            {{ formattedValue }}
          </span>
          
          <input
            ref="inputRef"
            :name="name"
            :value="formattedValue"
            @input="handleInput"
            @focus="handleFocus"
            @blur="handleBlur"
            type="text"
            inputmode="numeric"
            class="numeric-input border-1 rounded-lg px-3 py-2 font-medium focus:outline-none transition-colors h-[44px] text-light-grey mr-[12px] caret-primary-light"
            :class="isFocused ? 'border-2 border-primary text-black!' : 'border-light-grey'"
            :style="{ width: inputWidth }"
          />
          <span 
            class="transition-colors text-black text-[16px] font-[Inter] font-light"
          >
            {{ caption }}
          </span>
        </div>
      </label>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, nextTick } from 'vue'

const MAX_VALUE = Number.MAX_SAFE_INTEGER

const props = defineProps<{
  label: string
  name: string,
  caption: string
  thumb?: string
  modelValue?: number | null
}>()

const emit = defineEmits<{
  'update:modelValue': [value: number | null]
}>()

const inputRef = ref<HTMLInputElement | null>(null)
const measureRef = ref<HTMLSpanElement | null>(null)
const internalValue = ref<number | null>(props.modelValue ?? null)
const isFocused = ref(false)
const inputWidth = ref('72px')

const handleFocus = () => {
  isFocused.value = true
}

const handleBlur = () => {
  isFocused.value = false
}

watch(() => props.modelValue, (newVal) => {
  internalValue.value = newVal ?? null
})

const formattedValue = computed(() => {
  if (internalValue.value === null || internalValue.value === undefined) {
    return ''
  }
  const str = internalValue.value.toString()
  return str.replace(/\B(?=(\d{3})+(?!\d))/g, ' ')
})

const updateInputWidth = async () => {
  await nextTick()
  
  if (!formattedValue.value || !measureRef.value) {
    inputWidth.value = '72px'
    return
  }
  
  const textWidth = measureRef.value.offsetWidth
  const padding = 24 // px-3 = 12px * 2
  const border = isFocused.value ? 4 : 2 // border-2 or border-1
  const width = Math.max(72, textWidth + padding + border)
  
  inputWidth.value = `${width}px`
}

watch(formattedValue, updateInputWidth, { immediate: true })

const handleInput = (event: Event) => {
  const target = event.target as HTMLInputElement
  const rawValue = target.value.replace(/\s/g, '')
  
  if (rawValue === '') {
    internalValue.value = null
    emit('update:modelValue', null)
    return
  }
  
  if (!/^\d+$/.test(rawValue)) {
    nextTick(() => {
      if (inputRef.value) {
        inputRef.value.value = formattedValue.value
      }
    })
    return
  }
  
  const numValue = Number(rawValue)
  
  if (numValue > MAX_VALUE) {
    nextTick(() => {
      if (inputRef.value) {
        inputRef.value.value = formattedValue.value
      }
    })
    return
  }
  
  internalValue.value = numValue
  emit('update:modelValue', numValue)
}
</script>

<style scoped>
.numeric-input {
  transition: width 0.2s ease;
}

.avatar-focused {
  box-shadow: 0 0 0 3px white, 0 0 0 4px var(--color-primary);
}

.numeric-input::-webkit-outer-spin-button,
.numeric-input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

.numeric-input[type=number] {
  -moz-appearance: textfield;
}
</style>