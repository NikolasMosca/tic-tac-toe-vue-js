<template>
  <div id="app">
    <Interface v-bind:xScore="xScore" v-bind:oScore="oScore"/>
    <TrisGrid v-bind:winX="winX" v-bind:winO="winO"/>
  </div>
</template>

<script>
import TrisGrid from './components/TrisGrid.vue'
import Interface from './components/Interface.vue'

export default {
  name: 'app',
  data: function() {
    return {
      xScore: 0,
      oScore: 0
    }
  },
  methods: {
    //Function to increase X score
    winX: function() {
      this.xScore++
    },

    //Function to increase O score
    winO: function() {
      this.oScore++
    },
  },
  components: {
    TrisGrid,
    Interface
  }
}
</script>

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  body {
    background: url(./assets/background.jpg) center no-repeat;
  }
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    background: black;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  @media screen and (orientation:portrait) {
    #app {
      flex-direction: column;
    }
  }
  @media screen and (orientation:landscape) {
    #app {
      flex-direction: row;
    }
  }
</style>
