<template>
    <div class="seller" ref="seller">
        <div class="seller-content">
            <div class="overview">
                <h1 class="title">{{seller.name}}</h1>
                <div class="desc">
                    <star :size="36" :score="seller.score" class="star"></star>
                    <span class="text">({{seller.ratingCount}})</span>
                    <span class="text">月销{{seller.sellCount}}单</span>
                </div>
                <ul class="remark">
                    <li class="block">
                        <h2 class="title">起送价</h2>
                        <div class="content">
                            <span class="stress">{{seller.minPrice}}</span>元
                        </div>
                    </li>
                    <li class="block">
                        <h2 class="title">商家配送</h2>
                        <div class="content">
                            <span class="stress">{{seller.deliveryPrice}}</span>元
                        </div>
                    </li>
                    <li class="block">
                        <h2 class="title">平均配送时间</h2>
                        <div class="content">
                            <span class="stress">{{seller.deliveryTime}}</span>分钟
                        </div>
                    </li>
                </ul>
                <div class="favorite" @click="toggleFavorite">
                    <span class="icon-favorite" :class="{'active':favorite}"></span>
                    <span class="text">{{favoriteText}}</span>
                </div>
            </div>
            <split></split>
            <div class="bulletin">
                <h1 class="title">公告与活动</h1>
                <div class="content">
                    <p class="text">{{seller.bulletin}}</p>
                </div>
                <ul class="supports" v-if="seller.supports">
                    <li class="support-item border-b-1px" v-for="(item,index) in seller.supports"> 
                        <i class="icon" :class="classMap[seller.supports[index].type]"></i>
                        <span class="text">{{seller.supports[index].description}}</span>
                    </li>
                </ul>
            </div>
            <split></split>
            <div class="pics">
                <h1 class="title">商家实景</h1>
                <div class="pic-wrapper" ref="picWrapper">
                    <ul class="pic-list" ref="picList">
                        <li class="pic-item" v-for="pic in seller.pics">
                            <img :src="pic" width="120" height="90">
                        </li>
                    </ul>
                </div>
            </div>
            <split></split>
            <div class="info">
                <h1 class="title border-b-1px">商家信息</h1>
                <ul>
                    <li class="info-item border-b-1px" v-for="info in seller.infos">{{info}}</li>
                </ul>
            </div>
        </div>
    </div>
</template>
<script>
import Star from 'components/star/Star'
import Split from 'components/split/Split'
import BScroll from 'better-scroll'
export default {
    name: 'Seller',
    props: {
      seller: {
        type: Object
      }
    },
    data () {
        return {
            favorite: false
        }
    },
    computed: {
        favoriteText () {
            return this.favorite ? '已收藏' : '收藏'
        }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    },
    watch: {
      'seller'() {
        this.$nextTick(() => {
          this._initScroll()
           this._initPics()
        })
      }
    },
    mounted() {
      this.$nextTick(() => {
        this._initScroll()
         this._initPics()
      })
    },
    methods: {
        toggleFavorite (event) {
             if (!event._constructed) {
                return
                }
                this.favorite = !this.favorite
                saveToLocal(this.seller.id, 'favorite', this.favorite)
        },
        _initScroll() {
            if (!this.scroll) {
                this.scroll = new BScroll(this.$refs.seller, {
                    click: true 
                })
            } else {
                this.scroll.refresh()
                
            }
        },
        _initPics() {
            if(this.seller.pics) {
                let picwidth = 120
                let margin = 6
                let width = (picwidth+margin)*this.seller.pics.length-margin
                this.$refs.picList.style.width = width +'px'
                this.$nextTick(() => {
                    if(!this.picScroll) {
                        this.picScroll = new BScroll(this.$refs.picWrapper,{
                            scrollX: true,
                            eventPassthrough: 'vertical'
                        })
                    }else{
                        this.picScroll.refresh()
                    }
                })
            }
        }
    },
    components: {
        Star,
        Split
    }
}
</script>
<style lang="stylus" scoped>
@import "../../common/stylus/mixin.styl"
    .seller
        position absolute
        top 174px
        bottom 0
        left 0
        width 100%
        overflow hidden
        .overview
            position relative
            padding 18px
            .title
                line-height 14px
                margin-bottom 8px
                font-size 14px
                color rgb(7, 17, 27)
            .desc
                line-height 18px
                margin-bottom 18px
                font-size 0
                .star
                    display inline-block
                    vertical-align top
                    margin-right 8px
                .text
                    display inline-block
                    margin-right 12px
                    font-size 10px
                    color rgb(77, 85, 93)
            .remark
                display flex
                border-top 1px solid rgba(7, 17, 27, 0.1)
                .block
                    flex 1
                    text-align center
                    margin-top 18px
                    border-right 1px solid rgba(7, 17, 27, 0.1)
                    &:last-child
                        border none
                    .title
                        font-size 10px
                        line-height 10px
                        color rgb(147, 153, 159)
                        margin-bottom 4px
                    .content
                        line-height 24px
                        font-size 10px
                        color rgb(7, 17, 27)
                        .stress
                            font-size 24px
            .favorite
                position absolute
                width 50px
                right 11px
                top 18px
                text-align center
                .icon-favorite
                    display block
                    margin-bottom 4px
                    line-height 24px
                    font-size 24px
                    color #d4d6d9
                    &.active
                        color rgb(240, 20, 20)
                .text
                    line-height 10px
                    font-size 10px
                    color rgb(77, 85, 93)


        .bulletin
            padding 18px 18px 0 18px
            .title
                line-height 14px
                margin-bottom 8px
                font-size 14px
                color rgb(7, 17, 27)
            .content
                padding 0 12px 16px 12px
                .text
                    line-height 24px
                    font-size 12px
                    font-weight 200
                    color rgb(240, 20, 20)
            .supports
                .support-item
                    padding 16px 12px
                    border-b-1px(rgba(7, 17, 27, 0.1))
                    font-size 0
                    &:last-child
                        border none
                    .icon
                        display inline-block
                        width 16px
                        height 16px
                        vertical-align top
                        margin-right 6px
                        background-size 16px 16px
                        background-repeat no-repeat
                        &.decrease
                            bg-image('decrease_4')
                        &.discount
                            bg-image('discount_4')
                        &.guarantee
                            bg-image('guarantee_4')
                        &.invoice
                            bg-image('invoice_4')
                        &.special
                            bg-image('special_4')
                    .text
                        line-height 16px
                        font-size 12px
                        color rgb(7, 17, 27)
        .pics
            padding 18px
            .title
                line-height 14px
                margin-bottom 12px
                font-size 14px
                color rgb(7, 17, 27)
            .pic-wrapper
                width 100%
                overflow hidden
                white-space nowrap
                .pic-list
                    font-size 0
                    .pic-item
                        display inline-block
                        margin-right 6px
                        width 120px
                        height 90px
                        &:last-child
                            margin 0
        .info
            padding 18px 18px 0 18px
            .title
                line-height 14px
                padding-bottom 12px
                font-size 14px
                color rgb(7, 17, 27)
                border-b-1px(rgba(7, 17, 27, 0.1))
            .info-item
                padding 16px 12px
                line-height 16px
                font-size 12px
                color rgb(7, 17, 27)
                border-b-1px(rgba(7, 17, 27, 0.1))
                &:last-child
                    border-none()

                


        

                
</style>