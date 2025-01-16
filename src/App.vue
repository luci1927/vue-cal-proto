<template>
  <div id="app">
    <DataCalculatorComponent />
  </div>
</template>

<script>
import DataCalculatorComponent from './components/DataCalculatorComponent.vue';

export default {
  name: 'App',
  components: {
    DataCalculatorComponent,
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
