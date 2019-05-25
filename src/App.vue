<template>
  <div id="app">
      <h1>
          Quick overview of some of the most popular repositories on Github
      </h1>
    <GithubTable />
  </div>
</template>

<script>
import GithubTable from './components/GithubTable'

export default {
    name: 'app',
    components: {
        GithubTable
    }
}
</script>

<style scoped>
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
}
</style>
