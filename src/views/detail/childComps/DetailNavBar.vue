<template>
  <nav-bar>
    <div slot="center">
      <div v-for="item in titles">{{item}}</div>
    </div>
  </nav-bar>
</template>

<script>
  import NavBar from  'components/common/navbar/Navbar'

  export default {
    name: "DetailNavBar",
    components: {
      NavBar
    },
    data() {
        return {
          titles: ['商品', '参数', '评论', '推荐']
        }
      }
  }
</script>

<style scoped>

</style>
