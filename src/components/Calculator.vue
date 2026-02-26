<script setup lang="ts">
import { ref } from 'vue'
import { X, Minus, Plus, Divide, Equal, Delete } from 'lucide-vue-next'

const display = ref('0')
const expression = ref('')
const waitingForNextValue = ref(false)
const operator = ref<string | null>(null)
const previousValue = ref<number | null>(null)

const handleNumber = (num: string) => {
  if (waitingForNextValue.value) {
    display.value = num
    waitingForNextValue.value = false
  } else {
    // 雖然 CSS 會截斷，但程式邏輯也限制長度以維持效能
    if (display.value.length < 20) {
      display.value = display.value === '0' ? num : display.value + num
    }
  }
}

const handleOperator = (nextOperator: string) => {
  const inputValue = parseFloat(display.value)

  if (previousValue.value === null) {
    previousValue.value = inputValue
  } else if (operator.value) {
    const result = calculate(previousValue.value, inputValue, operator.value)
    display.value = String(result)
    previousValue.value = result
  }

  waitingForNextValue.value = true
  operator.value = nextOperator
  expression.value = `${previousValue.value} ${nextOperator}`
}

const calculate = (first: number, second: number, op: string) => {
  const result = (() => {
    switch (op) {
      case '+': return first + second
      case '-': return first - second
      case '*': return first * second
      case '/': return first / second
      default: return second
    }
  })()
  return Math.round(result * 100000000) / 100000000
}

const handleEqual = () => {
  const inputValue = parseFloat(display.value)

  if (operator.value && previousValue.value !== null) {
    const result = calculate(previousValue.value, inputValue, operator.value)
    display.value = String(result)
    previousValue.value = null
    operator.value = null
    expression.value = ''
    waitingForNextValue.value = true
  }
}

const clear = () => {
  display.value = '0'
  expression.value = ''
  previousValue.value = null
  operator.value = null
  waitingForNextValue.value = false
}

const deleteLast = () => {
  if (display.value.length > 1) {
    display.value = display.value.slice(0, -1)
  } else {
    display.value = '0'
  }
}

const toggleSign = () => {
  display.value = String(parseFloat(display.value) * -1)
}

const percent = () => {
  display.value = String(parseFloat(display.value) / 100)
}

const addDecimal = () => {
  if (!display.value.includes('.')) {
    display.value += '.'
  }
}

// 提取共用類名
const btnBase = "aspect-square rounded-[1.25rem] flex items-center justify-center transition-all duration-200 active:scale-95 select-none text-xl"
const btnNumber = `${btnBase} bg-sky-900/20 text-sky-100 font-medium border border-white/5 hover:bg-sky-800/40 hover:text-white`
const btnOperator = `${btnBase} bg-sky-500/10 text-sky-400 border border-white/5 hover:bg-sky-500/20 active:bg-sky-500 active:text-white`
const btnSpecial = `${btnBase} bg-sky-900/10 text-sky-500/70 font-bold border border-white/5 hover:bg-sky-800/30 hover:text-sky-300`
const btnEqual = `${btnBase} bg-gradient-to-br from-sky-400 via-sky-500 to-blue-600 text-white shadow-[0_10px_25px_rgba(56,189,248,0.4)] border-t border-white/20 hover:shadow-[0_15px_35px_rgba(56,189,248,0.6)] hover:brightness-110`
</script>

<template>
  <div class="flex-shrink-0" style="width: 380px; min-width: 380px; max-width: 380px;">
    <div class="relative group">
      <!-- 裝飾光暈：改為綠色系 -->
      <div class="absolute -inset-2 bg-gradient-to-br from-emerald-500/20 via-teal-500/20 to-green-500/20 rounded-[3rem] blur-3xl opacity-0 group-hover:opacity-100 transition-opacity duration-700"></div>
      
      <!-- 計算機主體 -->
      <div class="relative w-full bg-[#0F172A]/95 backdrop-blur-3xl p-8 rounded-[3rem] shadow-[0_30px_100px_rgba(0,0,0,0.5)] border border-white/10 overflow-hidden">
        
        <!-- 頂部裝飾條 -->
        <div class="flex justify-between items-center mb-8 px-1">
          <div class="flex space-x-2">
            <div class="w-3 h-3 rounded-full bg-[#FF5F56]"></div>
            <div class="w-3 h-3 rounded-full bg-[#FFBD2E]"></div>
            <div class="w-3 h-3 rounded-full bg-[#27C93F]"></div>
          </div>
          <span class="text-[10px] font-black text-slate-500 tracking-[0.4em] uppercase">Vortex Pro</span>
        </div>

        <!-- 螢幕區塊：關鍵在於固定 max-width 並處理溢出 -->
        <div class="w-full mb-8 bg-black/30 rounded-[1.5rem] p-6 border border-white/5 shadow-[inset_0_2px_10px_rgba(0,0,0,0.2)] overflow-hidden">
          <!-- 限制內容層的寬度，確保不撐開父層 -->
          <div class="max-w-full overflow-hidden">
            <div class="w-full h-6 text-sky-400/50 text-sm font-medium text-right truncate mb-1">
              {{ expression }}
            </div>
            <div class="w-full text-5xl font-extralight text-white text-right truncate tracking-tight leading-tight">
              {{ display }}
            </div>
          </div>
        </div>

        <!-- 按鍵網格 -->
        <div class="grid grid-cols-4 gap-4">
          <button @click="clear" :class="btnSpecial" class="text-xs">AC</button>
          <button @click="deleteLast" :class="btnSpecial"><Delete :size="20" /></button>
          <button @click="percent" :class="btnSpecial">%</button>
          <button @click="handleOperator('/')" :class="[btnOperator, operator === '/' ? 'bg-sky-500 text-white shadow-[0_0_25px_rgba(56,189,248,0.5)] border-sky-400' : '']">
            <Divide :size="22" stroke-width="2.5" />
          </button>

          <button @click="handleNumber('7')" :class="btnNumber">7</button>
          <button @click="handleNumber('8')" :class="btnNumber">8</button>
          <button @click="handleNumber('9')" :class="btnNumber">9</button>
          <button @click="handleOperator('*')" :class="[btnOperator, operator === '*' ? 'bg-sky-500 text-white shadow-[0_0_25px_rgba(56,189,248,0.5)] border-sky-400' : '']">
            <X :size="22" stroke-width="2.5" />
          </button>

          <button @click="handleNumber('4')" :class="btnNumber">4</button>
          <button @click="handleNumber('5')" :class="btnNumber">5</button>
          <button @click="handleNumber('6')" :class="btnNumber">6</button>
          <button @click="handleOperator('-')" :class="[btnOperator, operator === '-' ? 'bg-sky-500 text-white shadow-[0_0_25px_rgba(56,189,248,0.5)] border-sky-400' : '']">
            <Minus :size="22" stroke-width="2.5" />
          </button>

          <button @click="handleNumber('1')" :class="btnNumber">1</button>
          <button @click="handleNumber('2')" :class="btnNumber">2</button>
          <button @click="handleNumber('3')" :class="btnNumber">3</button>
          <button @click="handleOperator('+')" :class="[btnOperator, operator === '+' ? 'bg-sky-500 text-white shadow-[0_0_25px_rgba(56,189,248,0.5)] border-sky-400' : '']">
            <Plus :size="22" stroke-width="2.5" />
          </button>

          <button @click="toggleSign" :class="btnNumber"><span class="text-sm">+/-</span></button>
          <button @click="handleNumber('0')" :class="btnNumber">0</button>
          <button @click="addDecimal" :class="btnNumber">.</button>
          <button @click="handleEqual" :class="btnEqual">
            <Equal :size="24" stroke-width="3" />
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
