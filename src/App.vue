<template>
  <div id="app">
    <home-header :seller="seller"></home-header>
    <div class="tab border-t-1px border-b-1px">
      <router-link tag="div" class="tab-item" to="/goods">
        <span class="tab-link">商品</span>
      </router-link>
      <router-link tag="div" class="tab-item" to="/ratings">
        <span class="tab-link">评价</span>
      </router-link>
      <router-link tag="div" class="tab-item" to="/seller">
        <span class="tab-link">商家</span>
      </router-link>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script>
import HomeHeader from './components/header/Header' 
import axios from 'axios'

export default {
  name: 'App',
  components: {
    HomeHeader
  },
  data () {
    return {
      seller: {}
    }
  },
  methods: {
    // axios 请求seller 数据
    getHeaderInfo () {
      axios.get('/api/seller')
      .then(this.getHeaderInfoSucc)
    },
    getHeaderInfoSucc (res) {
      res = res.data
      if(res.ret && res.data) {
        const data = res.data
        this.seller = data
      }
    }
  },
  mounted () {
    this.getHeaderInfo()
  },
  
}
</script>

<style lang="stylus" scoped>
  @import '~stylus/mixin.styl'
  #app
    .tab
      display flex
      width 100%
      height 40px
      line-height 40px
      border-t-1px(rgba(7, 17, 27, 0.1))
      border-b-1px(rgba(7, 17, 27, 0.1))
      .tab-item
        flex 1
        text-align center
        display block
        font-size 14px
        .tab-link
          color rgb(77, 85, 93)
        &.router-link-exact-active
          .tab-link
            color rgb(240, 20, 20)

        
        
        
</style>
