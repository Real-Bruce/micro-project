<template>
  <router-view v-if="isRouterAlive">
    <Index msg="年会分组匹配小程序"/>
  </router-view>
</template>

<script>
import Index from './components/index.vue'
export default {
  name: 'App',
  components: {
    Index
  },
  provide() {
    return {
      reload: this.reload
    }
  },

  data() {
    return {
      isRouterAlive: true
    }
  },

  methods: {
    reload() {
      this.isRouterAlive = false;
      this.$nextTick(function () {
        this.isRouterAlive = true;
      });
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
//text-align: center; //color: #2c3e50; //margin-top: 20px; width: 100%;
}
</style>
