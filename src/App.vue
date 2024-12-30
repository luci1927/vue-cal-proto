<template>
  <div id="app">
    <CalculatorComponent />
  </div>
</template>

<script>
import CalculatorComponent from './components/CalculatorComponent.vue';

export default {
  name: 'App', // The name of this component should be 'App'
  components: {
    CalculatorComponent, // Import the CalculatorComponent here
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
