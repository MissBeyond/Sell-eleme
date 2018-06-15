<template>
    <div class="header">
        <div class="content-wrapper">
            <div class="avatar">
                <img width="64" height="64" :src="seller.avatar">
            </div>
            <div class="content">
                <div class="title">
                    <span class="brand"></span>
                    <div class="name">{{ seller.name }}</div>
                </div>
                <div class="description">
                    {{ seller.description }}/{{ seller.deliveryTime}}分钟送达
                </div>
                <!-- <div class="supports">
                    <span class="icon"></span>
                    <div class="text">{{ seller.supports[0].description }}</div>
                </div> -->
            </div>
        </div>
        <div class="bulletin-wrapper"></div>
    </div>
</template>
<script>
export default {
    name: 'HomeHeader',
    props: {
        seller: {
            type: Object
        }
    }
}
</script>
<style lang="stylus" scoped>

</style>


