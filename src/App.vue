<template>
  <div id="app">
    <router-view/>
    <div id="footer">
      <ul id="links">
        <li><a href="https://osu.ppy.sh/u/you06" target="_blank">My osu! profile</a></li>
        <li><a href="https://blog.tongmu.me/" target="_blank">Blog</a></li>
        <li><a href="https://github.com/you06/kps-tester" target="_blank">Github</a></li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style scoped>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}

#footer {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
}

#links li {
  display: inline-block;
  margin: 0 5px;
}

#links li a {
  color: #888888;
}
</style>
