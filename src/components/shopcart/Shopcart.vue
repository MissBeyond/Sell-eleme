<template>
    <div>
        <div class="shopcart">
            <div class="content">
                <div class="content-left">
                    <div class="logo-wrapper">
                        <div class="logo">
                            <i class="icon-shopping_cart"></i>
                        </div>
                    </div>
                    <div class="price">￥{{totalPrice}}</div>
                    <div class="desc">另需要配送费￥{{deliveryPrice}}元</div>
                </div>
                <div class="content-right">
                    <div class="pay">
                        ￥{{minPrice}}元配送
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    props: {
        selectFoods: {
            type: Array,
            default () {
                return []
            }
        },
        deliveryPrice: {
            type: Number,
            default: 0
        },
        minPrice: {
            type: Number,
            default: 0
        },
        computed: {
            totalPrice () {
                let total = 0
                this.selectFoods.forEach((food) => {
                    total += food.price * food.count
                })
                return total

            }
        }
    }
}
</script>
<style lang="stylus" scoped>
     .shopcart
        position: fixed
        left: 0
        bottom: 0
        z-index: 50
        width: 100%
        height: 48px
        .content
            display flex
            font-size 0
            background-color #141d27
            .content-left
                flex 1
                .logo-wrapper
                    display inline-block
                    position relative
                    vertical-align top
                    top -10px
                    margin 0 12px
                    padding 6px
                    width 56px
                    height 56px
                    box-sizing border-box
                    border-radius 50%
                    background-color #141d27
                    .logo
                        width 100%
                        height 100%
                        border-radius 50%
                        text-align center
                        background-color #2b343c
                        .icon-shopping_cart
                            font-size 24px
                            color #80858a
                            line-height 44px
                .price
                    display inline-block
                    vertical-align top
                    margin-top 12px
                    line-height 24px
                    padding-right 12px
                    box-sizing border-box
                    border-right 1px solid rgba(255, 255, 255, 0.1)
                    font-size 16px
                    font-weight 700
                    color rgba(255, 255, 255, 0.4)
                .desc
                    display inline-block
                    vertical-align top
                    line-height 24px
                    font-size 10px
                    margin 12px 0 0 12px
                    color rgba(255, 255, 255, 0.4)

            .content-right
                flex 0 0 105px
                width 105px
                .pay
                    height 48px
                    line-height 48px
                    font-size 12px
                    font-weight 700
                    text-align center
                    color rgba(255, 255, 255, 0.4)
                    background-color #2b333b



        
        

</style>


