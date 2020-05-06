<template>
  <div class="title-bar">
        <transition name="slide-down">
            <div class="title-wrapper" v-show="ifTitleAndMenuShow">
                <div class="left">
                    <span class="icon-chevron-left icon"></span>
                </div>
                <div class="right">
                    <div class="icon-wrapper">
                        <span class="icon-shopping-cart icon"></span>
                    </div>
                    <div class="icon-wrapper">
                        <span class="icon-user icon"></span>
                    </div>
                    <div class="icon-wrapper">
                        <span class="icon-dots-three-vertical icon"></span>
                    </div>
                </div>
            </div>
        </transition>
  </div>
</template>

<script>
export default {
    props: {
        ifTitleAndMenuShow: {
            type: Boolean,
            default: false
        }
    }
}
</script>

<style lang="scss" scoped>
@import "src/assets/styles/globe.scss";
.title-bar{
    .title-wrapper {
        position: absolute;
        top: 0;
        left: 0;
        z-index: 100;
        width: 100%;
        height: px2rem(48);
        background: white;
        box-shadow: 0 px2rem(4) px2rem(8) rgba($color: #000000, $alpha: 0.15);
        display: flex;
        @include center;

        .left{
            flex: 0 0 px2rem(60);
            margin-left: px2rem(8);
            .icon-chevron-left{
                font-size: px2rem(24);
            }
        }

        .right{
            flex: 1;
            justify-content: flex-end;
            display: flex;
            .icon-wrapper {
                flex: 0 0 px2rem(38);
                .icon-shopping-cart{
                    font-size: px2rem(22);
                }
            }
        }
    }
}
</style>