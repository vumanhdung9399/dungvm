<template>
  <div class="unit-stepper">
    <div class="unit-section">
      <span class="label">Unit</span>
      <div class="unit-toggle">
        <button @click="unit = '%'" :class="{ active: unit === '%' }">%</button>
        <button @click="unit = 'px'" :class="{ active: unit === 'px' }">px</button>
      </div>
    </div>

    <div class="value-section">
      <span class="label">Value</span>
      <div class="value-stepper">
        <button @click="decrement" :disabled="isMinusDisabled">-</button>
        <input 
          v-model="displayValue" 
          @blur="onBlur"
          @input="onInput"
        />
        <button @click="increment" :disabled="isPlusDisabled">+</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed, watch } from 'vue'

const unit = ref('%')
const value = ref(1.0)
const displayValue = ref(value.value.toString())

const isMinusDisabled = computed(() => {
  return parseFloat(displayValue.value) === 0
})
const isPlusDisabled = computed(() => {
  return unit.value === '%' && parseFloat(displayValue.value) === 100
})

watch(unit, (newUnit) => {
  if (newUnit === '%' && parseFloat(displayValue.value) > 100) {
    value.value = 100
    displayValue.value = '100'
  }
})

const sanitizeInput = (input: any) => {
  let sanitized = input.replace(',', '.')
  sanitized = sanitized.replace(/[^0-9.]/g, '')
  const parts = sanitized.split('.')
  if (parts.length > 2) {
    sanitized = parts[0] + '.' + parts.slice(1).join('')
  }
  return sanitized
}

const onInput = (e: any) => {
  displayValue.value = sanitizeInput(e.target.value)
}

const onBlur = () => {
  let num = parseFloat(displayValue.value)
  if (isNaN(num) || num < 0) {
    num = 0
  }
  if (unit.value === '%' && num > 100) {
    num = 100
  }
  value.value = num
  displayValue.value = num.toString()
}

const increment = () => {
  let num = parseFloat(displayValue.value) || 0
  let numIncrement = ++num
  if ((unit.value === '%' && numIncrement <= 100) || unit.value === 'px') {
    value.value = numIncrement
    displayValue.value = numIncrement.toFixed(2).replace(/\.?0+$/, '')
  } else if (unit.value === '%' && numIncrement >= 100) {
    displayValue.value = '100'
  }
}

const decrement = () => {
  let num = parseFloat(displayValue.value) || 0
  let numDecrement = --num
  if (numDecrement >= 0) {
    value.value = numDecrement
    displayValue.value = numDecrement.toFixed(2).replace(/\.?0+$/, '')
  } else {
    displayValue.value = '0' 
  }
}
</script>

<style scoped>
.unit-stepper {
  display: flex;
  flex-direction: column;
  background-color: #121212;
  padding: 16px;
  color: #ccc;
  font-family: 'Inter', sans-serif;
  height: calc(100vh - 0px);
}

.label {
  margin-bottom: 6px;
  font-size: 13px;
  color: #aaa;
}

.unit-section,
.value-section {
  margin-bottom: 16px;
}

.unit-toggle {
  display: flex;
  gap: 4px;
  background-color: #1e1e1e;
  border-radius: 10px;
  padding: 4px;
  width: 165px;
}
.unit-toggle button {
  flex: 1;
  background-color: transparent;
  border: none;
  padding: 8px 16px;
  color: #aaa;
  font-size: 14px;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.2s;
}
.unit-toggle .active {
  background-color: #3a3a3a;
  color: white;
}

.value-stepper {
  display: flex;
  align-items: center;
  background-color: #1e1e1e;
  padding: 6px 8px;
  border-radius: 10px;
  gap: 12px;
  width: fit-content;
}
.value-stepper button {
  background-color: transparent;
  color: white;
  border: none;
  padding: 4px 12px;
  font-size: 18px;
  border-radius: 6px;
  cursor: pointer;
}
.value-stepper button[disabled] {
  opacity: 0.3;
  cursor: not-allowed;
}
.value-stepper input {
  background-color: transparent;
  border: none;
  color: white;
  text-align: center;
  width: 60px;
  font-size: 16px;
  outline: none;
}

</style>