<template>
    <div class="ratingselect">
        <div class="rating-type border-b-1px">
            <span class="block positive" :class="{'active':selectType === 2}">{{desc.all}}<span class="count">47</span></span>
            <span class="block positive" :class="{'active':selectType === 0}">{{desc.positive}}<span class="count">7</span></span>
            <span class="block nagative" :class="{'active':selectType === 1}">{{desc.negative}}<span class="count">18</span></span>
        </div>
        <div class="switch">
            <i class="icon-check_circle"></i>
            <span class="text">评价内容</span>
        </div>
    </div>
</template>
<script>
const POSITIVE = 0
const NEGATIVE = 1
const ALL = 2
export default {
    props: {
        ratings: {
            type: Array,
            default () {
                return []
            }
        },
        selectType: {
            type: Number,
            default: ALL
        },
        onlyContent: {
            type: Boolean,
            default: false
        },
        desc: {
            type: Object,
            default () {
                return {
                    all: '全部',
                    positive: '推荐',
                    negative: '不满意'
                }

            }
        }
    }
}
</script>
<style lang="stylus" scoped>
    @import "../../common/stylus/mixin.styl"
    .ratingselect
        .rating-type
            padding 18px 0
            margin 0 18px
            border-b-1px(rgba(7, 17, 27, 0.1))
            .block
                display inline-block
                padding 8px 12px
                margin-right 8px
                line-height 16px
                border-radius 1px
                font-size 12px
                color rgb(77, 85, 93)
                &.active
                    color #ffffff
                .count
                    font-size 8px
                    margin-left 2px
                &.positive
                    background-color rgba(0, 160, 220, 0.2)
                    &.active
                        background-color rgb(0, 160, 220)
                &.nagative
                    background-color rgba(77, 85, 93, 0.2)
                    &.active
                        background-color rgb(77, 85, 93)
        .switch
            padding 12px 18px
            line-height 24px
            border-bottom 1px solid rgba(7, 17, 27, 0.1)
            color rgb(147, 153, 159)
            font-size 0
            .icon-check_circle
                display inline-block
                vertical-align top
                margin-right 4px
                font-size 24px
            .text
                display inline-block
                font-size 12px


</style>

