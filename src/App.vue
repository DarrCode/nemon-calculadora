<template>
  <div id="app">
    <Calculadora />
  </div>
</template>

<script>
import Calculadora from './components/Calculadora.vue'

export default {
  name: 'App',
  components: {
    Calculadora
  }
}
</script>

<style>
body{
  background:#E1E1E1;
}

#app {
  margin-top: 50px;
  font-family: 'Nunito Sans', sans-serif;
}

.primary {
  background-color: #2196F3!important;
}

.color-danger {
  color: #E53935!important;
}

.p-0 {
  padding: 1px!important;
}

.welcome {
  margin-bottom: 10px!important;
}

.btn-back {
  margin-bottom: 15px;
}
</style>
