<template>
  <div id="app">
    <DateCalculatorComponent />
  </div>
</template>

<script>
import DateCalculatorComponent from './components/DateCalculatorComponent.vue';

export default {
  name: 'App', // The name of this component should be 'App'
  components: {
    DateCalculatorComponent, // Import the CalculatorComponent here
  },
};
</script>

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #f5f5f5;
}
</style>
