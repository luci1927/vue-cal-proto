<template>
  <div id="app">
    <DateCalculatorComponent />
  </div>
</template>

<script>
import DateCalculatorComponent from './components/DateCalculatorComponent.vue';

export default {
  name: 'App',
  components: {
    DateCalculatorComponent,
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
