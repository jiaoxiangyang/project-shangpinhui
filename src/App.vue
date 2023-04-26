<template>
  <div>
    <Header></Header>
    <!-- 路由组件出口的地方 -->
    <router-view></router-view>
    <!-- 在Home，Search页是显示的，在登陆，注册是隐藏的 -->
    <Footer v-show="$route.meta.showFooter"></Footer>
  </div>
</template>

<script>
//引入
import Header from "./components/Header";
import Footer from "./components/Footer";
export default {
  name: "App",
  components: { Header, Footer },
};
</script>

<style>
</style>