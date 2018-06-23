<template>
    <transition name="move">
        <div class="food" v-show="showFlag" ref="food">
            <div class="food-content">
                <div class="image-header">
                    <img :src="food.image">
                    <div class="back" @click="hide">
                        <i class="icon-arrow_lift"></i>
                    </div>
                </div>
                <div class="content">
                    <h1 class="title">{{food.name}}</h1>
                    <div class="detail">
                        <span class="count">月售{{food.sellCount}}份</span><span class="rating">好评率{{food.rating}}%</span>
                    </div>
                    <div class="price">
                        <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                    </div>
                    <div class="cartcontrol-wrapper">
                        <cartcontrol :food="food"></cartcontrol>
                    </div>
                    <div @click.stop.prevent="addFirst" class="buy" v-show="!food.count ||food.count === 0">
                        加入购物车
                    </div>
                </div>
                <split v-show="food.info"></split>
                <div class="info" v-show="food.info">
                    <h1 class="title">商品信息</h1>
                    <p class="text">{{food.info}}</p>
                </div>
                <split></split>
                <div class="rating">
                    <h1 class="title">商品评价</h1>
                    <ratingselect @select="selectRating" @toggle="toggleContent" :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.fatings"></ratingselect>
                </div>
            </div>
        </div>
    </transition>
</template>
<script>
import BScroll from 'better-scroll'//引进这个实现上下滑动的插件  
import Cartcontrol from 'components/cartcontrol/Cartcontrol'
import Vue from 'vue'
import Split from 'components/split/Split'
import Ratingselect from 'components/ratingselect/Ratingselect'

const POSITIVE = 0
const NEGATAIVE = 1
const ALL = 2

export default {
    props: {
        food: {
            type: Object
        }
    },
    data () {
        return {
            showFlag: false,
            selectType: ALL,
            onlyContent: true,
            desc: {
                all: '全部',
                positive: '推荐',
                negative: '吐槽'
            }
            }
    },
    methods: {
        show () {
            this.showFlag = true
            this.selectType = 2
            this.onlyContent = true
            this.$nextTick(() =>{
                if(!this.scroll) {
                    this.scoll = new BScroll(this.$refs.food,{
                        click: true
                    })
                }else{
                    this.scroll.refresh()
                }
            })
        },
        hide () {
            this.showFlag = false
        },
        addFirst (event) {
            if(!event._constructed) {
                return
            }
            Vue.set(this.food, 'count', 1)
        },
        selectRating (type) {
            this.selectType = type
            this.$nextTick(() => {
                this.scroll.refresh()
            })
        },
        toggleContent () {
            this.onlyContent = !this.onlyContent
            this.$nextTick(() => {
                this.scroll.refresh()
            })
        }
        


    },
    components: {
        Cartcontrol,
        Split,
        Ratingselect,
        
    }
}
</script>
<style lang="stylus" scoped>
    .food
        position fixed
        left 0
        top 0
        bottom 48px
        z-index 30
        width 100%
        background-color #fff
        transform: translate3d(0, 0, 0)
        &.move-enter-active, &.move-leave-active
            transition: all 0.2s linear
        &.move-enter, &.move-leave-active
            transform: translate3d(100%, 0, 0)
        .image-header
            position relative
            height 0
            width 100%
            padding-top 100%
            img
                position absolute
                top 0
                left 0
                width 100%
                height 100%
            .back
                position absolute
                top 10px
                left 0
                .icon-arrow_lift
                    display block
                    padding 10px
                    font-size 20px
                    color #ffffff
        .content
            position relative
            padding 18px
            .title
                line-height 14px
                font-size 14px
                font-weight 700
                color rgb(7, 17, 27)
                margin-bottom 8px
            .detail
                margin-bottom 18px
                height 10px
                line-height 10px
                .count,.rating
                    font-size 10px
                    color rgb(147, 153, 159)
                .count
                    margin-right 12px
            .price
                font-weight 700
                line-height 24px
                .now
                    margin-right 8px
                    font-size 14px
                    color rgb(240, 20, 20)
                .old
                    text-decoration line-through
                    font-size 10px
                    color rgb(147, 153, 159)
            .cartcontrol-wrapper
                position absolute
                right 12px
                bottom 12px
            .buy
                position absolute
                right 18px
                bottom 18px
                z-index 10
                height 24px
                line-height 24px
                padding 0 12px
                box-sizing border-box
                border-radius 12px
                font-size 10px
                color #ffffff
                background-color rgb(0, 120, 220)
        .info
            padding 18px
            .title
                line-height 14px
                margin-bottom 6px
                font-size 14px
                color rgb(7, 17, 27)
            .text
                padding 0 8px
                font-size 12px
                font-weight 200
                line-height 24px
                color rgb(77, 85, 93)
        .rating
            padding-top 18px
            .title
                line-height 14px
                margin-left 18px
                font-size 14px
                color rgb(7, 17, 27)
            


        
</style>

