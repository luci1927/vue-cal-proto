<template>
    <div class="calculator">
      <div class="calculator-header">
        <div class="menu-icon">☰</div>
        <div class="calculator-type">Data Calculator</div>
      </div>
      
      <div class="display-area">
        <input type="number" v-model="inputValue" class="main-display" />
        <select v-model="inputUnit" class="unit-select">
          <option value="bytes">Bytes</option>
          <option value="kilobytes">Kilobytes</option>
          <option value="megabytes">Megabytes</option>
          <option value="gigabytes">Gigabytes</option>
        </select>
      </div>
  
      <div class="results-grid">
        <div class="result-item">
          <span class="result-label">Bytes:</span>
          <span class="result-value">{{ results.bytes }}</span>
        </div>
        <div class="result-item">
          <span class="result-label">Kilobytes:</span>
          <span class="result-value">{{ results.kb }}</span>
        </div>
        <div class="result-item">
          <span class="result-label">Megabytes:</span>
          <span class="result-value">{{ results.mb }}</span>
        </div>
        <div class="result-item">
          <span class="result-label">Gigabytes:</span>
          <span class="result-value">{{ results.gb }}</span>
        </div>
      </div>
    </div>
  </template>
  
<script>
export default {
  name: 'DataCalculatorComponent',
  data() {
    return {
      inputValue: 0,
      inputUnit: 'bytes',
      precision: 2
    }
  },
  computed: {
    results() {
      const value = parseFloat(this.inputValue) || 0
      const baseValue = this.convertToBytes(value, this.inputUnit)
      
      return {
        bytes: this.formatNumber(baseValue),
        kb: this.formatNumber(baseValue / 1024),
        mb: this.formatNumber(baseValue / (1024 ** 2)),
        gb: this.formatNumber(baseValue / (1024 ** 3))
      }
    }
  },
  methods: {
    convertToBytes(value, unit) {
      const multipliers = {
        bytes: 1,
        kilobytes: 1024,
        megabytes: 1024 ** 2,
        gigabytes: 1024 ** 3
      }
      return value * multipliers[unit]
    },
    formatNumber(value) {
      if (value < 0.01 && value > 0) {
        return value.toExponential(this.precision)
      }
      return value.toFixed(this.precision)
    }
  }
}
</script>

<style scoped>
  .calculator {
    width: 320px;
    margin: 40px auto;
    background: #f0f0f0;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    overflow: hidden;
  }
  
  .calculator-header {
    display: flex;
    align-items: center;
    padding: 8px 12px;
    background: #e6e6e6;
  }
  
  .menu-icon {
    font-size: 18px;
    margin-right: 12px;
    cursor: pointer;
  }
  
  .calculator-type {
    font-size: 14px;
    color: #333;
  }
  
  .display-area {
    padding: 20px;
    background: #f9f9f9;
  }
  
  .main-display {
    width: 100%;
    padding: 10px;
    font-size: 24px;
    border: none;
    background: transparent;
    text-align: right;
    margin-bottom: 10px;
  }
  
  .unit-select {
    width: 100%;
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
    background: white;
  }
  
  .results-grid {
    padding: 15px;
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
    background: #f9f9f9;
  }
  
  .result-item {
    padding: 10px;
    background: white;
    border-radius: 4px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  }
  
  .result-label {
    font-size: 12px;
    color: #666;
    display: block;
  }
  
  .result-value {
    font-size: 16px;
    color: #333;
    display: block;
    margin-top: 4px;
  }
  </style>