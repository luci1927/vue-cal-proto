<template>
  <div id="app">
    <DataCalculatorComponent />
  </div>
</template>

<script>
import DataCalculatorComponent from './components/DataCalculatorComponent.vue';

export default {
  name: 'App', // The name of this component should be 'App'
  components: {
    DataCalculatorComponent, // Import the CalculatorComponent here
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
