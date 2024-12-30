<template>
  <div class="calculator-container" :class="{ 'dark-mode': isDarkMode }">
    <div class="calculator" :class="{ 'scientific': isScientificMode }">
      <div class="header">
        <div class="mode-toggle">
          <button class="mode-btn" :class="{ active: !isScientificMode }" @click="isScientificMode = false">Standard</button>
          <button class="mode-btn" :class="{ active: isScientificMode }" @click="isScientificMode = true">Scientific</button>
        </div>
        <button class="theme-btn" @click="toggleTheme">
          {{ isDarkMode ? '☀️' : '🌙' }}
        </button>
      </div>

      <div class="display">
        <div class="previous-operand">{{ previousOperand }} {{ operation }}</div>
        <div class="current-operand">{{ currentOperand || '0' }}</div>
      </div>

      <div class="buttons-grid" :class="{ 'scientific': isScientificMode }">
        <!-- Scientific Functions (only visible in scientific mode) -->
        <template v-if="isScientificMode">
          <button class="operation-btn" @click="handleDegRad">{{ inDegrees ? 'DEG' : 'RAD' }}</button>
          <button class="operation-btn" @click="handleFunction('sin')">sin</button>
          <button class="operation-btn" @click="handleFunction('cos')">cos</button>
          <button class="operation-btn" @click="handleFunction('tan')">tan</button>

          <button class="operation-btn" @click="handleFunction('log')">log</button>
          <button class="operation-btn" @click="handleFunction('ln')">ln</button>
          <button class="operation-btn" @click="handlePi">π</button>
          <button class="operation-btn" @click="handleFunction('e')">e</button>

          <button class="operation-btn" @click="handleFunction('asin')">sin⁻¹</button>
          <button class="operation-btn" @click="handleFunction('acos')">cos⁻¹</button>
          <button class="operation-btn" @click="handleFunction('atan')">tan⁻¹</button>
          <button class="operation-btn" @click="handleOperator('^')">^</button>
        </template>

        <!-- Standard Calculator Buttons -->
        <button class="operation-btn" @click="handlePercent">%</button>
        <button class="operation-btn" @click="handleClearEntry">CE</button>
        <button class="operation-btn" @click="handleClear">C</button>
        <button class="operation-btn" @click="handleBackspace">⌫</button>

        <button class="operation-btn" @click="handleInverse">1/x</button>
        <button class="operation-btn" @click="handleSquare">x²</button>
        <button class="operation-btn" @click="handleSquareRoot">√x</button>
        <button class="operation-btn" @click="handleOperator('÷')">÷</button>

        <button class="number-btn" @click="appendNumber('7')">7</button>
        <button class="number-btn" @click="appendNumber('8')">8</button>
        <button class="number-btn" @click="appendNumber('9')">9</button>
        <button class="operation-btn" @click="handleOperator('×')">×</button>

        <button class="number-btn" @click="appendNumber('4')">4</button>
        <button class="number-btn" @click="appendNumber('5')">5</button>
        <button class="number-btn" @click="appendNumber('6')">6</button>
        <button class="operation-btn" @click="handleOperator('-')">-</button>

        <button class="number-btn" @click="appendNumber('1')">1</button>
        <button class="number-btn" @click="appendNumber('2')">2</button>
        <button class="number-btn" @click="appendNumber('3')">3</button>
        <button class="operation-btn" @click="handleOperator('+')">+</button>

        <button class="operation-btn" @click="handlePlusMinus">±</button>
        <button class="number-btn" @click="appendNumber('0')">0</button>
        <button class="number-btn" @click="appendDecimal">.</button>
        <button class="equals-btn" @click="handleEquals">=</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CalculatorComponent',
  data() {
    return {
      currentOperand: '',
      previousOperand: '',
      operation: null,
      shouldResetScreen: false,
      isDarkMode: false,
      isScientificMode: false,
      inDegrees: true
    }
  },
  methods: {
    toggleTheme() {
      this.isDarkMode = !this.isDarkMode
      localStorage.setItem('calculatorTheme', this.isDarkMode ? 'dark' : 'light')
    },
    handleDegRad() {
      this.inDegrees = !this.inDegrees
    },
    toRadians(degrees) {
      return degrees * (Math.PI / 180)
    },
    toDegrees(radians) {
      return radians * (180 / Math.PI)
    },
    handleFunction(func) {
      if (this.currentOperand === '') return
      const number = parseFloat(this.currentOperand)
      let result

      switch (func) {
        case 'sin':
          result = Math.sin(this.inDegrees ? this.toRadians(number) : number)
          break
        case 'cos':
          result = Math.cos(this.inDegrees ? this.toRadians(number) : number)
          break
        case 'tan':
          result = Math.tan(this.inDegrees ? this.toRadians(number) : number)
          break
        case 'asin':
          result = this.inDegrees ? this.toDegrees(Math.asin(number)) : Math.asin(number)
          break
        case 'acos':
          result = this.inDegrees ? this.toDegrees(Math.acos(number)) : Math.acos(number)
          break
        case 'atan':
          result = this.inDegrees ? this.toDegrees(Math.atan(number)) : Math.atan(number)
          break
        case 'log':
          result = Math.log10(number)
          break
        case 'ln':
          result = Math.log(number)
          break
        case 'e':
          result = Math.E
          break
      }
      this.currentOperand = result.toString()
    },
    handlePi() {
      this.currentOperand = Math.PI.toString()
    },
    appendNumber(number) {
      if (this.shouldResetScreen) {
        this.currentOperand = ''
        this.shouldResetScreen = false
      }
      if (number === '0' && this.currentOperand === '0') return
      if (this.currentOperand.length >= 16) return
      this.currentOperand = this.currentOperand.toString() + number.toString()
    },
    appendDecimal() {
      if (this.shouldResetScreen) {
        this.currentOperand = '0'
        this.shouldResetScreen = false
      }
      if (this.currentOperand.includes('.')) return
      this.currentOperand = this.currentOperand + '.'
    },
    handleOperator(operator) {
      if (this.currentOperand === '') return
      if (this.previousOperand !== '') {
        this.calculate()
      }
      this.operation = operator
      this.previousOperand = this.currentOperand
      this.shouldResetScreen = true
    },
    calculate() {
      let computation
      const prev = parseFloat(this.previousOperand)
      const current = parseFloat(this.currentOperand)
      if (isNaN(prev) || isNaN(current)) return

      switch (this.operation) {
        case '+':
          computation = prev + current
          break
        case '-':
          computation = prev - current
          break
        case '×':
          computation = prev * current
          break
        case '÷':
          if (current === 0) {
            alert("Cannot divide by zero")
            return
          }
          computation = prev / current
          break
        case '^':
          computation = Math.pow(prev, current)
          break
        default:
          return
      }

      this.currentOperand = computation.toString()
      this.operation = null
      this.previousOperand = ''
      this.shouldResetScreen = true
    },
    handleClear() {
      this.currentOperand = ''
      this.previousOperand = ''
      this.operation = null
    },
    handleClearEntry() {
      this.currentOperand = ''
    },
    handleBackspace() {
      this.currentOperand = this.currentOperand.toString().slice(0, -1)
    },
    handleEquals() {
      this.calculate()
    },
    handlePlusMinus() {
      if (this.currentOperand === '') return
      this.currentOperand = (-parseFloat(this.currentOperand)).toString()
    },
    handlePercent() {
      if (this.currentOperand === '') return
      this.currentOperand = (parseFloat(this.currentOperand) / 100).toString()
    },
    handleSquare() {
      if (this.currentOperand === '') return
      const number = parseFloat(this.currentOperand)
      this.currentOperand = (number * number).toString()
    },
    handleSquareRoot() {
      if (this.currentOperand === '') return
      const number = parseFloat(this.currentOperand)
      if (number < 0) {
        alert("Cannot calculate square root of negative number")
        return
      }
      this.currentOperand = Math.sqrt(number).toString()
    },
    handleInverse() {
      if (this.currentOperand === '' || parseFloat(this.currentOperand) === 0) {
        alert("Cannot divide by zero")
        return
      }
      this.currentOperand = (1 / parseFloat(this.currentOperand)).toString()
    }
  },
  mounted() {
    const savedTheme = localStorage.getItem('calculatorTheme')
    if (savedTheme) {
      this.isDarkMode = savedTheme === 'dark'
    } else {
      this.isDarkMode = window.matchMedia('(prefers-color-scheme: dark)').matches
    }
  }
}
</script>

<style scoped>
.calculator-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #f0f0f0;
  transition: background-color 0.3s ease;
}

.calculator-container.dark-mode {
  background-color: #1a1a1a;
}

.calculator {
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  padding: 20px;
  width: 320px;
  transition: all 0.3s ease;
}

.calculator.scientific {
  width: 480px;
}

.dark-mode .calculator {
  background-color: #2d2d2d;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.mode-toggle {
  display: flex;
  gap: 8px;
}

.mode-btn {
  padding: 6px 12px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  background-color: #f0f0f0;
  color: #000000;
  transition: all 0.2s;
}

.dark-mode .mode-btn {
  background-color: #3d3d3d;
  color: #ffffff;
}

.mode-btn.active {
  background-color: #0067c0;
  color: white;
}

.dark-mode .mode-btn.active {
  background-color: #0078d4;
}

.theme-btn {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  padding: 5px;
  border-radius: 50%;
  transition: background-color 0.2s;
}

.display {
  background-color: #ffffff;
  padding: 20px;
  text-align: right;
  min-height: 100px;
  transition: all 0.3s ease;
  margin-bottom: 10px;
}

.dark-mode .display {
  background-color: #2d2d2d;
}

.previous-operand {
  color: #666666;
  font-size: 1.2rem;
  min-height: 1.5rem;
}

.dark-mode .previous-operand {
  color: #999999;
}

.current-operand {
  color: #000000;
  font-size: 2.5rem;
  font-weight: bold;
  word-wrap: break-word;
  word-break: break-all;
}

.dark-mode .current-operand {
  color: #ffffff;
}

.buttons-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px;
}

.buttons-grid.scientific {
  grid-template-columns: repeat(4, 1fr);
}

button {
  border: none;
  border-radius: 4px;
  font-size: 1.25rem;
  padding: 15px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.number-btn {
  background-color: #ffffff;
  color: #000000;
}

.dark-mode .number-btn {
  background-color: #3d3d3d;
  color: #ffffff;
}

.number-btn:hover {
  background-color: #f0f0f0;
}

.dark-mode .number-btn:hover {
  background-color: #4d4d4d;
}

.operation-btn {
  background-color: #f9f9f9;
  color: #000000;
  font-size: 1.1rem;
}

.dark-mode .operation-btn {
  background-color: #333333;
  color: #ffffff;
}

.operation-btn:hover {
  background-color: #e8e8e8;
}

.dark-mode .operation-btn:hover {
  background-color: #404040;
}

.equals-btn {
  background-color: #0067c0;
  color: white;
}

.dark-mode .equals-btn {
  background-color: #0078d4;
}

.equals-btn:hover {
  background-color: #005aa6;
}

.dark-mode .equals-btn:hover {
  background-color: #0066b4;
}
</style>