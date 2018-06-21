<template>
    <div class="goods">
        <div class="menu-wrapper" ref="menuWrapper">
            <ul>
                <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex === index}" @click="selectMenu(index,$event)">  
                    <span class="text border-b-1px">
                        <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
                    </span>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper" ref="foodsWrapper">
            <ul>
                <li v-for="item in goods" class="food-list" ref="foodList">
                    <h1 class="title">{{item.name}}</h1>
                    <ul>
                        <li v-for="food in item.foods" class="food-item border-b-1px">
                            <div class="icon">
                                <img width="57px" height="57px" :src="food.icon">
                            </div>
                            <div class="content">
                                <h2 class="name">{{food.name}}</h2>
                                <p class="desc">{{food.description}}</p>
                                <div class="extra">
                                    <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                                </div>
                                <div class="price">
                                    <span class="now">￥{{food.price}}</span>
                                    <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                                </div>
                                <div class="cartcontrol-wrapper">
                                    <cartcontrol :food="food"></cartcontrol>
                                </div>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
        <shopcart ref:shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    </div>
</template>
<script>
import axios from 'axios'
import BScroll from 'better-scroll'//引进这个实现上下滑动的插件  
import Shopcart from 'components/shopcart/Shopcart'
import Cartcontrol from 'components/cartcontrol/Cartcontrol'
export default {
    name: 'Goods',
    props: {
        seller: {
            type: Object
        }
    },
    data () {
        return {
            goods: [],//用来接收从后台返回的数据 
            listHeight: [],//存放右边每一个li的高度 
            scrollY: 0//实时滚动的y轴大小，利用better-scroll的onScroll事件监听这个值
            
        }
    },
    components: {
        Shopcart,
        Cartcontrol
    },
    created() {
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']//定义一个变量 
    },
    computed: {
        //利用vue中的计算属性computed实时计算，对listHeight遍历，返回当前的左边mune-wrapper的li  
        //对应的index，从而让其显示高亮的属性.current  
        currentIndex() {
            for(let i = 0; i < this.listHeight.length; i++) {
                let height1 = this.listHeight[i]
                let height2 = this.listHeight[i + 1]
                //当遍历到listHeight最后一个元素的时候，height2的值为undefined,如果是  
                //最后一个元素的话!height2为真，后面就不需要判断了 
                if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
                    return i
                }
            }
            return 0//默认情况下是返回第一个元素  
        },
        selectFoods () {
            let foods = []
            this.goods.forEach((good) => {
                good.foods.forEach((food) => {
                    if(food.count) {
                        foods.push(food)
                    }
                })
            })
            return foods
        }
    },
    methods: {
        //点击左边的menu，跳到右边相应的li 
        selectMenu (index,event) {
            //浏览器的事件对象是没有_constructed属性，当是浏览器触发的时候就return  
            //而用better-scroll自定义的事件触发的时候有这个属性，为true  
            //用这个属性，就是避免在浏览器点击的时候，触发两次点击的效果 
            if (!event._constructed) {
                return
            }
            //点击左侧的菜单项的时候，右边跳到相应的内容区域
            let foodList = this.$refs.foodList//获取到右边li对象
            let ref = foodList[index]//根据index，获取到右边具体滚动到的li
            this.foodsWrapperScroll.scrollToElement(ref, 300)//要滑动到右边的对象，300完成动作的时间
            
        },
        addFood(target) {
            this._drop(target)
        },
        _drop(target) {
            // 体验优化,异步执行下落动画
            this.$nextTick(() => {
            this.$refs.shopcart.drop(target)
            });
        },
        getGoodsInfo () {
            axios.get('/api/goods')
            .then(this.getGoodsInfoSucc)
            // .then(this._calculateHeight)
            
        },
        getGoodsInfoSucc (res) {
            res = res.data
            if(res.ret && res.data) {
                const data = res.data
                this.goods = data
                this.$nextTick(() => {
                    //better-scroll的实例初始化要放在vm.$nextTick()里面才生效
                    this._initScroll()
                    this._calculateHeight()
                })
            }
        },
        _initScroll () {//初始化需要滚动的对象  
            //初始化需要滚动的对象,通过vm.$refs可以获取到在<template>中设置ref=menuWrapper属性的元素节点
            this.menuWrapperScroll = new BScroll(this.$refs.menuWrapper,{
                click: true
            })
            this.foodsWrapperScroll = new BScroll(this.$refs.foodsWrapper,{
                click: true,
                probeType:3//设置实时监听滚动的位置的效果的属性  
            })
            //监听右侧滚动区域，左边相应的menu高亮  
            //监听滚动事件scroll，实时改变this.scrollY的值，  
            // pos是元素所处的位置，内部自动传的  
            this.foodsWrapperScroll.on('scroll', (pos) => {
                //scrollY是自定义的变量，用于存储滚动的位置  
                //Math.round(pos.y)是一个负数
                this.scrollY = Math.abs( Math.round(pos.y) )
            })
        },
        //将右侧的.foods-wrapper里面的每个li的高度进行累加，存放到数组listHeight里面
         _calculateHeight () {
            let foodList = this.$refs.foodList
            let height = 0
            this.listHeight.push(height)//第一个元素的高度是0 
            for( let i = 0; i < foodList.length; i++) {
                let item = foodList[i]
                height += item.clientHeight
                this.listHeight.push(height)
            }
        }
    },
    mounted () {
        this.getGoodsInfo()        
    }
}
</script>
<style lang="stylus" scoped>
    @import "../../common/stylus/mixin.styl"
    .goods
        display flex
        position absolute
        overflow hidden
        width 100%
        top 174px
        bottom 46px
        .menu-wrapper
            flex 0 0 80px
            width 80px
            background-color #f3f5f7
            .menu-item
                display table
                width 56px
                height 54px
                line-height 14px
                padding 0 12px
                &.current 
                    position relative
                    z-index 10
                    margin-top -1px
                    background-color #fff
                    font-weight 700
                    .text
                        border-none()
                .icon
                    display inline-block
                    vertical-align top
                    width 12px
                    height 12px
                    margin-right 2px
                    background-size 12px 12px
                    background-repeat no-repeat
                    &.decrease
                        bg-image('decrease_3')
                    &.discount
                        bg-image('discount_3')
                    &.guarantee
                        bg-image('guarantee_3')
                    &.invoice
                        bg-image('invoice_3')
                    &.special
                        bg-image('special_3')
                    
                .text
                    display table-cell
                    width 56px
                    vertical-align middle
                    font-size 12px
                    border-b-1px(rgba(7, 17, 27, 0.1))
                    


        .foods-wrapper
            flex 1
            .title
                padding-left 14px
                font-size 12px
                height 26px
                line-height 26px
                border-left 2px #d9dde1 solid 
                color rgb(143, 153, 159)
                background-color #f3f5f7
            .food-item
                display flex
                margin 18px
                padding-bottom 18px
                border-b-1px(rgba(7, 17, 27, 0.1))
                &:last-child
                    border-none()
                    margin-bottom 0
                .icon
                    flex 0 0 57px
                    margin-right 10px
                .content
                    flex 1
                    .name
                        font-size 14px
                        margin 2px 0 8px 0
                        line-height 14px
                        color rgb(7, 17, 27)
                    .desc
                        font-size 10px
                        margin-bottom 8px
                        color rgb(147, 153, 159)
                    .extra
                        font-size 10px
                        color rgb(147, 153, 159)
                        line-height 12px
                        .count
                            margin-right 12px
                    .price
                        font-weight 700
                        line-height 24px
                        .now
                            margin-left 8px
                            font-size 14px
                            color rgb(240, 20, 20)
                        .old
                            text-decoration line-through
                            font-size 10px
                            color rgb(147, 153, 159)
                    .cartcontrol-wrapper
                        position absolute
                        right 0
                        bottom 12px
                    



</style>

