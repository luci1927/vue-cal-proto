// DateCalculator.vue
<template>
  <div class="calculator-container" :class="{ 'dark-mode': isDarkMode }">
    <div class="calculator">
      <div class="header">
        <div class="title-bar">
          <span class="calculator-title">Date Calculator</span>
          <button class="theme-btn" @click="toggleTheme">
            {{ isDarkMode ? '☀️' : '🌙' }}
          </button>
        </div>
      </div>

      <div class="calculator-content">
        <!-- Mode Selection -->
        <div class="mode-toggle">
          <button 
            v-for="mode in modes" 
            :key="mode"
            class="mode-btn"
            :class="{ active: currentMode === mode }"
            @click="currentMode = mode"
          >
            {{ mode }}
          </button>
        </div>

        <!-- Difference Between Dates -->
        <div v-if="currentMode === 'Difference'" class="date-section">
          <div class="input-group">
            <label>Start Date</label>
            <input 
              type="date" 
              v-model="startDate"
              class="date-input"
            >
          </div>
          <div class="input-group">
            <label>End Date</label>
            <input 
              type="date" 
              v-model="endDate"
              class="date-input"
            >
          </div>
          <div class="result-section">
            <div class="result-item">
              <span class="label">Days:</span>
              <span class="value">{{ dateDifference.days }}</span>
            </div>
            <div class="result-item">
              <span class="label">Weeks:</span>
              <span class="value">{{ dateDifference.weeks }}</span>
            </div>
            <div class="result-item">
              <span class="label">Months:</span>
              <span class="value">{{ dateDifference.months }}</span>
            </div>
            <div class="result-item">
              <span class="label">Years:</span>
              <span class="value">{{ dateDifference.years }}</span>
            </div>
          </div>
        </div>

        <!-- Add/Subtract Days -->
        <div v-if="currentMode === 'Add/Subtract'" class="date-section">
          <div class="input-group">
            <label>Start Date</label>
            <input 
              type="date" 
              v-model="baseDate"
              class="date-input"
            >
          </div>
          <div class="operation-group">
            <select v-model="operation" class="operation-select">
              <option value="add">Add</option>
              <option value="subtract">Subtract</option>
            </select>
            <input 
              type="number" 
              v-model="amount" 
              class="number-input"
              min="0"
            >
            <select v-model="unit" class="unit-select">
              <option value="days">Days</option>
              <option value="weeks">Weeks</option>
              <option value="months">Months</option>
              <option value="years">Years</option>
            </select>
          </div>
          <div class="result-section">
            <div class="result-item">
              <span class="label">Result Date:</span>
              <span class="value">{{ calculatedDate }}</span>
            </div>
          </div>
        </div>

        <!-- Weekday Finder -->
        <div v-if="currentMode === 'Weekday'" class="date-section">
          <div class="input-group">
            <label>Select Date</label>
            <input 
              type="date" 
              v-model="weekdayDate"
              class="date-input"
            >
          </div>
          <div class="result-section">
            <div class="result-item">
              <span class="label">Day of Week:</span>
              <span class="value">{{ weekdayResult }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DateCalculator',
  data() {
    return {
      isDarkMode: false,
      modes: ['Difference', 'Add/Subtract', 'Weekday'],
      currentMode: 'Difference',
      startDate: new Date().toISOString().split('T')[0],
      endDate: new Date().toISOString().split('T')[0],
      baseDate: new Date().toISOString().split('T')[0],
      operation: 'add',
      amount: 1,
      unit: 'days',
      weekdayDate: new Date().toISOString().split('T')[0]
    }
  },
  computed: {
    dateDifference() {
      const start = new Date(this.startDate)
      const end = new Date(this.endDate)
      const diffTime = Math.abs(end - start)
      const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24))
      
      return {
        days: diffDays,
        weeks: Math.floor(diffDays / 7),
        months: Math.floor(diffDays / 30.44),
        years: Math.floor(diffDays / 365.25)
      }
    },
    calculatedDate() {
      const date = new Date(this.baseDate)
      const amount = this.operation === 'add' ? this.amount : -this.amount

      switch (this.unit) {
        case 'days':
          date.setDate(date.getDate() + amount)
          break
        case 'weeks':
          date.setDate(date.getDate() + (amount * 7))
          break
        case 'months':
          date.setMonth(date.getMonth() + amount)
          break
        case 'years':
          date.setFullYear(date.getFullYear() + amount)
          break
      }

      return date.toLocaleDateString('en-US', {
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric'
      })
    },
    weekdayResult() {
      const date = new Date(this.weekdayDate)
      return date.toLocaleDateString('en-US', { weekday: 'long' })
    }
  },
  methods: {
    toggleTheme() {
      this.isDarkMode = !this.isDarkMode
      localStorage.setItem('calculatorTheme', this.isDarkMode ? 'dark' : 'light')
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
  background-color: #f5f5f5;
  transition: all 0.3s ease;
  padding: 20px;
}

.calculator-container.dark-mode {
  background-color: #1f1f1f;
}

.calculator {
  background-color: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 12px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  padding: 24px;
  width: 480px;
  transition: all 0.3s ease;
}

.dark-mode .calculator {
  background-color: rgba(45, 45, 45, 0.95);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
}

.header {
  margin-bottom: 20px;
}

.title-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.calculator-title {
  font-size: 1.25rem;
  font-weight: 600;
  color: #1f1f1f;
}

.dark-mode .calculator-title {
  color: #ffffff;
}

.mode-toggle {
  display: flex;
  gap: 8px;
  background-color: #f0f0f0;
  padding: 4px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.dark-mode .mode-toggle {
  background-color: #333333;
}

.mode-btn {
  flex: 1;
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  font-size: 0.9rem;
  font-weight: 500;
  background: transparent;
  color: #666666;
  cursor: pointer;
  transition: all 0.2s ease;
}

.mode-btn.active {
  background-color: #ffffff;
  color: #0078d4;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.dark-mode .mode-btn {
  color: #999999;
}

.dark-mode .mode-btn.active {
  background-color: #424242;
  color: #60cdff;
}

.date-section {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.input-group label {
  font-size: 0.9rem;
  color: #666666;
}

.dark-mode .input-group label {
  color: #999999;
}

.date-input {
  padding: 12px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  font-size: 1rem;
  background-color: #ffffff;
  color: #1f1f1f;
  transition: all 0.2s ease;
}

.dark-mode .date-input {
  background-color: #333333;
  border-color: #404040;
  color: #ffffff;
}

.operation-group {
  display: flex;
  gap: 8px;
}

.operation-select,
.unit-select,
.number-input {
  padding: 12px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  font-size: 1rem;
  background-color: #ffffff;
  color: #1f1f1f;
}

.dark-mode .operation-select,
.dark-mode .unit-select,
.dark-mode .number-input {
  background-color: #333333;
  border-color: #404040;
  color: #ffffff;
}

.number-input {
  width: 100px;
}

.result-section {
  background-color: #f8f8f8;
  padding: 16px;
  border-radius: 8px;
}

.dark-mode .result-section {
  background-color: #2d2d2d;
}

.result-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 0;
  border-bottom: 1px solid #e0e0e0;
}

.dark-mode .result-item {
  border-bottom-color: #404040;
}

.result-item:last-child {
  border-bottom: none;
}

.result-item .label {
  color: #666666;
  font-size: 0.9rem;
}

.result-item .value {
  color: #1f1f1f;
  font-weight: 600;
}

.dark-mode .result-item .label {
  color: #999999;
}

.dark-mode .result-item .value {
  color: #ffffff;
}

.theme-btn {
  background: transparent;
  border: none;
  font-size: 1.25rem;
  padding: 8px;
  cursor: pointer;
  border-radius: 50%;
  transition: all 0.2s ease;
}

.theme-btn:hover {
  background-color: rgba(0, 0, 0, 0.05);
}

.dark-mode .theme-btn:hover {
  background-color: rgba(255, 255, 255, 0.1);
}
</style>