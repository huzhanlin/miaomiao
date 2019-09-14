<template>
  <div id="main">
    <Header title="我的喵喵"></Header>
    <div id="content">
      <login></login>
    </div>
    <Tabbar></Tabbar>
  </div>
</template>

<script>
import Header from "@/components/Header";
import Tabbar from "@/components/TabBar";
import login from "@/components/Login";
export default {
  name: "Mine",
  components: {
    Header,
    Tabbar,
    login
  }
};
</script>
<style scoped>
</style>

