<template>
  <div>
    <h1>params参数------{{ $route.params.keyWord }}============{{keyWord}}</h1>
    <h1>query参数-------{{ $route.query.keyWord }}======={{k}}</h1>
  </div>
</template>

<script>
export default {
  //路由组件可以传递props
  props: ["keyWord","k"],
};
</script>

<style>
</style>