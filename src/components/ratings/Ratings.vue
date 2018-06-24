<template>
    <div class="ratings" ref="ratings">
        <div class="ratings-content">
            <div class="overview">
                <div class="overview-left">
                    <h1 class="score">{{seller.score}}</h1>
                    <div class="title">综合评分</div>
                    <div class="rank">{{seller.rankRate}}%</div>
                </div>
                <div class="overview-right">
                    <div class="score-wrapper">
                        <span class="title">服务态度</span>
                        <div class="star">
                            <star :score="seller.serviceScore" :size="36" ></star>
                        </div>
                        <span class="score">{{seller.serviceScore}}</span>
                    </div>
                    <div class="score-wrapper">
                        <span class="title">商品评分</span>
                        <div class="star">
                            <star :score="seller.foodScore" :size="36" ></star>
                        </div>
                        <span class="score">{{seller.foodScore}}</span>
                    </div>
                    <div class="delivery-wrapper">
                        <span class="title">送达时间</span>
                        <span class="delivery">{{seller.deliveryTime}}分钟</span>
                    </div>
                </div>
            </div>
            <split></split>
            <ratingselect @select="selectRating" @toggle="toggleContent" :select-type="selectType" :only-content="onlyContent" :ratings="ratings"></ratingselect>
            <div class="rating-wrapper">
                <ul>
                    <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in ratings" class="rating-item">
                        <div class="avatar">
                            <img :src="rating.avatar" width="28" height="28">
                        </div>
                        <div class="content">
                            <h1 class="title">{{rating.username}}</h1>
                            <div class="star-wrapper">
                                <star :size="24" :score="rating.score"></star>
                                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
                            </div>
                            <p class="text">{{rating.text}}</p>
                            <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                                <i class="icon-thumb_up"></i>
                                <span class="item" v-for="item in rating.recommend">{{item}}</span>
                            </div>
                            <div class="time">
                                {{rating.rateTime | formatDate}}
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>
<script>
const ALL = 2
import axios from 'axios'
import BScroll from 'better-scroll'//引进这个实现上下滑动的插件  
import Star from 'components/star/Star'
import Split from 'components/split/Split'
import {formatDate} from 'common/js/date'
import Ratingselect from 'components/ratingselect/Ratingselect'

export default {
    name: 'Ratings',
    props: {
        seller: {
            type: Object
        },
        food: {
            type: Object
        }
    },
    data () {
        return {
            ratings: [],
            selectType: ALL,
            onlyContent: true,

        }
    },
    components: {
        Star,
        Split,
        Ratingselect
    },
    methods: {
        selectRating (type) {
            this.selectType = type
            this.$nextTick(() =>{
                this.scroll.refresh()
            })
        },
        toggleContent () {
            this.onlyContent = !this.onlyContent
            this.scroll.refresh()
        },
        needShow (type,text) {
            if(this.onlyContent && !text) {
                return false
            }
            if(this.selectType === ALL) {
                return true
            }
            else{
                return type === this.selectType
            }
        },
        getRatingsInfo () {
            axios.get('/api/ratings')
            .then(this.getRatingsInfoSucc) 

        },
        getRatingsInfoSucc (res) {
            res =res.data
            if(res.ret = res.data) {
                const data = res.data
                this.ratings = data
                this.$nextTick(() => {
                    this.scroll = new BScroll(this.$refs.ratings, {
                    click: true
                    })
                })
            }

        }
    },
    mounted () {
        this.getRatingsInfo()
    },
     filters: {
        formatDate(time) {
            let date = new Date(time)
            return formatDate(date,'yyyy-MM-dd hh:mm')
        }
    },

}
</script>
<style lang="stylus" scoped>
    .ratings
        position absolute
        top 174px
        bottom 0
        width 100%
        left 0
        overflow hidden
        .overview
            display flex
            padding 18px 0
            .overview-left
                flex 0 0 137px
                padding 6px 0
                width 137px
                border-right 1px solid rgba(7, 17, 27, 0.1)
                text-align center
                .score
                    margin-bottom 6px
                    font-size 24px
                    line-height 28px
                    color rgb(255, 153, 0)
                .title
                    margin-bottom 8px
                    font-size 12px
                    line-height 12px
                    color rgb(7, 17, 27)
                .rank
                    font-size 10px
                    line-height 10px
                    color rgb(147, 153, 159)
            .overview-right
                flex 1
                padding-left 24px
                .score-wrapper
                    margin-bottom 8px
                    font-size 0
                    .title
                        display inline-block
                        vertical-align top
                        margin-right 12px
                        font-size 12px
                        line-height 18px
                        color rgb(7, 17, 27)
                    .star
                        display inline-block
                    .score
                        display inline-block
                        vertical-align top
                        margin-left 12px
                        font-size 12px
                        line-height 18px
                        color rgb(255, 153, 0)
                .delivery-wrapper
                    font-size 0
                    .title
                        margin-right 12px
                        font-size 12px
                        line-height 18px
                        color rgb(7, 17, 27)
                    .delivery
                        font-size 12px
                        line-height 18px
                        color rgb(147, 153, 159)
        .rating-wrapper
            padding 0 18px
            .rating-item
                display flex
                padding 18px 0
                border-bottom 1px solid rgba(7, 17, 27, 0.1)
                .avatar
                    flex 0 0 28px
                    width 28px
                    height 28px
                    margin-right 12px
                    img
                        border-radius 50%
                .content
                    position relative
                    flex 1
                    .title
                        margin-bottom 4px
                        line-height 12px
                        font-size 10px
                        color rgb(7, 17, 27)
                    .star-wrapper
                        margin-bottom 6px
                        font-size 0
                        .star
                            display inline-block
                            vertical-align top
                            margin-right 6px
                        .delivery
                            display inline-block
                            line-height 12px
                            font-size 10px
                            font-weight 200
                            color rgb(147, 153, 159)
                    .text
                        margin-bottom 8px
                        line-height 18px
                        font-size 12px
                        color rgb(7, 17, 27)
                    .recommend
                        line-height 16px
                        font-size 0
                        .icon-thumb_up,.item
                            display inline-block
                            margin 0 8px 4px 0
                            font-size 9px
                        .icon-thumb_up
                            color rgb(0, 160, 220)
                        .item
                            padding 0 6px
                            color rgb(147, 153, 159)
                            border 1px solid rgba(7, 17, 27, 0.1)
                            border-radius 1px
                            background-color #fff
                    .time
                        position absolute
                        top 0
                        right 0
                        line-height 12px
                        font-size 10px
                        font-weight 200
                        color rgb(147, 153, 159)



                        
                    


</style>