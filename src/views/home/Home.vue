<template>
  <div id="home">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>

    <scroll class="content"
            ref="scroll"
            :probe-type="3"
            @scroll="contentScroll"
            :pull-up-load="true"
            @pullingUp="loadMore">
        <home-swiper :banners="banners"/>
        <recommend-view :recommends="recommends"/>
        <feature-view/>
        <tab-control
          :titles="['流行', '新款', '精选']"
          @tabClick="tabClick"/>
        <goods-list :goods="showGoods"/>
      </scroll>

    <back-top  @click.native="backClick" v-show="isShow"/>

  </div>
</template>

<script>
  //子组件
  import HomeSwiper from './childComps/HomeSwiper'
  import RecommendView from './childComps/RecommendView'
  import FeatureView from './childComps/FeatureView'

  //公共组件
  import NavBar from 'components/common/navbar/NavBar'
  import TabControl from 'components/content/tabControl/TabControl'
  import GoodsList from 'components/content/goods/GoodsList'
  import scroll from 'components/common/scroll/Scroll'
  import BackTop from 'components/content/backTop/BackTop'

  import {getHomeMultidata, getHomeGoods} from "network/home";


  export default {
    name: "Home",
    components: {
      HomeSwiper,
      RecommendView,
      FeatureView,
      NavBar,
      TabControl,
      GoodsList,
      scroll,
      BackTop
    },
    computed: {
      showGoods() {
        return this.goods[this.currentType].list
      }
    },
    data() {
        return {
          banners: [],
          recommends:[],
          goods: {
            'pop': {page: 0, list: []},
            'new': {page: 0, list: []},
            'sell': {page: 0, list: []}
          },
          currentType: 'pop',
          isShow: false
        }
      },
    created() {
      //请求多个数据
      this.getHomeMultidata()
      //请求商品数据
      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')
    },
    mounted() {
      //监听item中图片加载完成
      this.$bus.$on('itemImageLoad', () => {
        this.$refs.scroll.refresh()
      })
    },
    methods: {
      /**
       *事件监听相关方法
       */
      tabClick(index) {
        switch (index) {
          case 0:
            this.currentType = 'pop'
            break
          case 1:
            this.currentType = 'new'
            break
          case 2:
            this.currentType = 'sell'
            break
        }
      },
      backClick() {
        this.$refs.scroll.scrollTo(0, 0)
      },
      contentScroll(position) {
        this.isShow = -position.y > 1000
      },
      loadMore() {
        this.getHomeGoods(this.currentType)
      },
     /**
      *网络请求相关方法
      */
      getHomeMultidata() {
        getHomeMultidata().then(res => {
          this.banners = res.data.banner.list
          this.recommends = res.data.recommend.list
        })
      },

      getHomeGoods(type) {
        const page = this.goods[type].page + 1
        getHomeGoods(type, page).then(res => {
          this.goods[type].list.push(...res.data.list)
          this.goods[type].page += 1

          this.$refs.scroll.finishPullUp()
        })
      }
    }
  }
</script>

<style scoped>
  #home {
    height: 100vh;
  }

  .home-nav {
    background-color: var(--color-tint);
    color: #fff;
  }

  /*.content {*/
  /*  height: calc(100% - 93px);*/
  /*  overflow: hidden;*/
  /*}*/

  .content {
    position: absolute;
    top: 44px;
    bottom: 49px;
    right: 0;
    overflow: hidden;
  }

</style>
