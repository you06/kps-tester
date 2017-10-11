<template>
  <div class="hello">
    <img id="logo" src="../assets/logo.png">
    <h1>{{ title }}</h1>
    <h2>{{ prologue }}</h2>
    <router-link id="start-btn" to="/main">Go!</router-link>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      title: 'KPS Tester',
      prologue: 'How fast can you be'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

#start-btn {
  font-size: 40px;
  text-decoration: none;
}

#logo {
  height: 100px;
}
</style>
