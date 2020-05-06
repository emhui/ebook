<template>
  <div class="menu-bar">
        <transition name="slide-up">
            <div class="bottom-wrapper" v-show="ifTitleAndMenuShow"
            :class="{'hide-box-shadow': ifSettingShow || !ifTitleAndMenuShow}">
                <div class="icon-wrapper">
                    <span class="icon-menu icon" @click="showSetting(3)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-swap icon" @click="showSetting(2)" ></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-adjust icon" @click="showSetting(1)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-a icon" @click="showSetting(0)">A</span>
                </div>
            </div>
        </transition>
        <transition name="slide-up">
            <div class="setting-wrapper" v-show="ifSettingShow">
                <div class="setting-font" v-if="showTag === 0">
                    <div class="preview" :style="{fontSize: fontSizeList[0].fontSize + 'px'}">A</div>
                    <div class="select-wrapper" v-for="(item, index) in fontSizeList" :key="index" @click="setFontSize(item.fontSize)">
                        <div class="line"></div>
                        <div class="point-wrapper">
                            <div class="point" v-show="defaultFontSize === item.fontSize">
                                <div class="small"></div>
                            </div>
                        </div>
                        <div class="line"></div>
                    </div>
                    <div class="preview" :style="{fontSize: fontSizeList[fontSizeList.length - 1].fontSize + 'px'}">A</div>
                </div>

                <div class="setting-theme" v-else-if="showTag === 1">
                    <div class="setting-theme-item" v-for="(item, index) in themeList" :key="index" @click="setTheme(index)">
                        <div class="preview" :style="{'background': item.style.body.background}"></div>
                        <div class="text" :class="{'selected': index === defaultTheme}">{{item.name}}</div>
                    </div>
                </div>

                <div class="setting-progress" v-else-if="showTag === 2">
                    <div class="progress-wrapper">
                        <a-slider :default-value="30"
                        :tip-formatter="null"
                        :disabled="!bookAvailable"
                        @afterChange="afterProgressChange"
                        @change="onProgressChange"
                        />
                    </div>
                    <div class="text-wrapper">
                        <div class="text">{{ bookAvailable ? progress + '%' : '加载中'}}</div>
                    </div>
                </div>
            </div>
        </transition>
        <div class="toc-draw">
            <a-drawer
                title="目录"
                placement="left"
                :closable="false"
                :visible="showTag === 3"
                @close="onClose"
                >
                <div v-if="bookAvailable" >
                    <p v-for="(item, index) in nav.toc" :key="index" @click="navTo(item.href)">
                        {{item.label}}
                    </p>
                </div>
                <div v-if="!bookAvailable" class="loading-wrapper"><a-spin /></div>
            </a-drawer>
        </div>
  </div>
</template>

<script>
export default {
    props: {
        ifTitleAndMenuShow: {
            type: Boolean,
            default: false
        },
        fontSizeList: Array,
        defaultFontSize: Number,
        themeList: Array,
        defaultTheme: Number,
        bookAvailable: {
            type: Boolean,
            default: false
        },
        nav: Object
    },
    data() {
        return {
            ifSettingShow: false,
            showTag: 0,
            progress: 0,
            disabled: false
        }
    },
    methods: {
        showSetting(tag) {
            if (tag !== 3) {
                this.ifSettingShow = true
            }
            this.showTag = tag
        },
        hideSetting() {
            this.ifSettingShow = false
        },
        setFontSize(fontSize) {
            this.$parent.setFontSize(fontSize)
        },
        setTheme(index) {
            this.$parent.setTheme(index)
        },
        onProgressInput(progress) {
            console.log(progress)
        },
        formatter(value) {
            return `${value}%`
        },
        afterProgressChange(value) {
            this.$parent.onProgressChange(value)
        },
        onProgressChange(value) {
            this.progress = value
        },
        onClose() {
            this.showTag = -1
            this.$parent.hideSettingAndMenu()
        },
        navTo(href) {
            this.$parent.navTo(href)
        }
    }
}
</script>

<style lang="scss" scoped>
@import "src/assets/styles/globe.scss";
.menu-bar{
    .setting-wrapper{
        position: absolute;
        bottom: px2rem(48);
        left: 0;
        z-index: 100;
        width: 100%;
        height: px2rem(60);
        background: white;
        box-shadow: 0 px2rem(-4) px2rem(8) rgba($color: #000000, $alpha: 0.15);

        .setting-font{
            display: flex;
            height: 100%;
            .preview{
                flex: 0 0 px2rem(40);
                @include center;
            }

            .select-wrapper{
                flex: 1;
                display: flex;
                align-items: center;

                .line{
                    flex: 1;
                    height: 0;
                    border-top: px2rem(1) solid;
                    color: #ccc;
                }

                .point-wrapper{
                    flex: 0 0 0;
                    width: 0;
                    height: px2rem(8);
                    border-left: px2rem(1) solid;
                    color: #ccc;
                    position: relative;
                    .point{
                        position: absolute;
                        top: px2rem(-6);
                        left: px2rem(-6);
                        width: px2rem(20);
                        height: px2rem(20);
                        border-radius: 50%;
                        background: white;
                        border: px2rem(1) #ccc solid;
                        box-shadow: px2rem(1) px2rem(1) px2rem(1) rgba(0,0,0,.15);
                        @include center;
                        .small{
                            width: px2rem(8);
                            height: px2rem(8);
                            background: black;
                            border-radius: 50%;
                        }
                    }
                }
            }
        }
        .setting-theme{
            display: flex;
            height: 100%;

            &-item{
                flex: 1;
                display: flex;
                flex-direction: column;
                box-sizing: border-box;
                padding: px2rem(10);
                .preview{
                    flex: 1;
                    box-sizing: border-box;
                    border: px2rem(1) solid #ccc;

                }

                .text{
                    font-size: px2rem(10);
                    color: #999;
                    @include center;

                    &.selected{
                        color: #333;
                    }
                }
            }
        }
        .setting-progress{
            display: flex;
            height: 100%;
            width: 100%;
            flex-direction: column;
            @include center;

            .progress-wrapper{
                width: 100%;
                padding: 0 px2rem(20);
            }

            .text-wrapper{

            }
        }
    }
    .bottom-wrapper{
        position: absolute;
        bottom: 0;
        left: 0;
        z-index: 100;
        width: 100%;
        height: px2rem(48);
        background: white;
        box-shadow: 0 px2rem(-4) px2rem(8) rgba($color: #000000, $alpha: 0.15);
        display: flex;

        &.hide-box-shadow{
            box-shadow: none;
        }

        .icon-wrapper{
            flex: 1;
            @include center;
        }
    }
}
.loading-wrapper{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    transform: translate(50%, 50%);
    height: 100%;
}
</style>