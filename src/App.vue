<template>
  <div id="app">
    <TheCanvas />
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    
  </div>
</template>

<script>
//import HelloWorld from './components/HelloWorld.vue'
import TheCanvas from './components/TheCanvas.vue'

export default {
  name: 'App',
  components: {
    // HelloWorld,
    TheCanvas,
  }
}
</script>
