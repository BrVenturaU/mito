<template>
  <div id="app" class="container">
    <Header title="Multi Language Interactive Tool"/>
    <BaseContainer></BaseContainer>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import BaseContainer from './components/BaseContainer.vue'


export default {
  name: 'App',
  components: {
    Header,
    BaseContainer
  },
  created(){
    document.title = "MITO - Multi Language Interactive Tool"
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
</style>
