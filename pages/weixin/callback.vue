<template>
  <!-- header -->
  <div></div>
  <!-- footer -->
</template>
<script>
export default {
  layout: "empty",
  data() {
    return {};
  },
  mounted() {
    //1.获取参数
    let token = this.$route.query.token;
    let name = this.$route.query.name;
    let openid = this.$route.query.openid;
    //调用父级别的vue方法
    window.parent["loginCallback"](name, token, openid);
  },
};
</script>