<template>
  <div id="app">
      <left></left>
      <div class="main">
         <router-view></router-view>
      </div>
  </div>
</template>

<script>
import left from '@/components/common/left'
import pos from  '@/components/page/pos'
export default {
  name: 'App',
  components:{
   left
  }
}
</script>

<style>
/* #app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0px;
} */
.main{
  float: left;
  width: 95%;
  background-color: #e8f2f7;
  height: 100%;
  overflow: auto;
}
</style>
