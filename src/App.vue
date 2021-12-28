<template>
  <router-view/>
  <div class="footer" id="nav">
    <div class="footer_nav">
      <router-link to="/">レジアプリ</router-link> |
      <router-link to="/Edit">メニュー作成</router-link>|
      <router-link to="/about">ヘルプ</router-link>
    </div>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.footer{
  text-align:center;
  position:fixed;
  margin:0 auto;
  bottom:0;
  background-color:black;
  width:100%;
  height:auto;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: rgba(250, 224, 177, 0.452);
}

#nav a.router-link-exact-active {
  color: rgb(250, 237, 177);
}
</style>
