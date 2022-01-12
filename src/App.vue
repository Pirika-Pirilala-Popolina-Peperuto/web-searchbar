<template>
  <div id="app">
    <SearchBar/>
  </div>
</template>

<script>
import SearchBar from './components/SearchBar.vue'

export default {
  name: 'App',
  components: {
    SearchBar
  }
}
</script>

<style lang="scss">
$background: #7caeff00;

body {
  margin: 0;
  padding: 0;
  width: 100%;
}

#app {
  background: $background;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  width: 100%;
  
}
</style>
