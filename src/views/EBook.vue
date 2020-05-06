<template>
    <div>
        <title-bar :ifTitleAndMenuShow="ifTitleAndMenuShow"></title-bar>
        <div class="ebook">
            <div class="reader-wrapper">
                <div id="area"></div>
                <div class="mask">
                    <div class="left" @click="prePage"></div>
                    <div class="mid" @click="toggleTitleAndMenu"></div>
                    <div class="right" @click="nextPage"></div>
                </div>
            </div>
        </div>
        <menu-bar ref="menuBar"
        :nav="nav"
        :bookAvailable="bookAvailable"
        :defaultTheme="defaultTheme"
        :themeList="themeList"
        :defaultFontSize="defaultFontSize"
        :fontSizeList="fontSizeList"
        :ifTitleAndMenuShow="ifTitleAndMenuShow"
        ></menu-bar>
    </div>
</template>

<script>
import ePub from 'epubjs'
const DOWNLOAD_URL = '/DS.epub'
export default {
    mounted() {
        this.loadEpub()
    },
    data() {
        return {
            ifTitleAndMenuShow: false,
            fontSizeList: [
                {
                    fontSize: 12
                },
                {
                    fontSize: 14
                },
                {
                    fontSize: 16
                },
                {
                    fontSize: 18
                },
                {
                    fontSize: 20
                },
                {
                    fontSize: 22
                },
                {
                    fontSize: 24
                }
            ],
            defaultFontSize: 16,
            themeList: [
                {
                    name: 'default',
                    style: {
                        body: {
                            color: '#000',
                            background: '#fff'
                        }
                    }
                },
                {
                    name: 'eye',
                    style: {
                        body: {
                            color: '#000',
                            background: '#99CC66'
                        }
                    }
                },
                {
                    name: 'night',
                    style: {
                        body: {
                            color: '#fff',
                            background: '#000'
                        }
                    }
                },
                {
                    name: 'gold',
                    style: {
                        body: {
                            color: '#fff',
                            background: '#CCCC00'
                        }
                    }
                }
            ],
            defaultTheme: 0,
            bookAvailable: false,
            nav: null
        }
    },
    methods: {
        loadEpub() {
        this.book = ePub(DOWNLOAD_URL)
        this.rendition = this.book.renderTo('area', { method: 'default', width: window.innerWidth, height: window.innerHeight })
        this.themes = this.rendition.themes
        this.setFontSize(this.defaultFontSize)
        this.registerTheme()
        this.setTheme(this.defaultTheme)
        // 获取location
        this.book.ready.then(() => {
            return this.book.locations.generate()
        }).then(result => {
            this.nav = this.book.navigation
            this.locations = this.book.locations
            this.bookAvailable = true
            console.log('导入' + this.bookAvailable)
        })
        this.rendition.display()
        },
        prePage() {
        if (this.rendition) {
            this.rendition.prev()
        }
        },
        nextPage() {
        if (this.rendition) {
            this.rendition.next()
        }
        },
        toggleTitleAndMenu() {
            this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow
            if (!this.ifTitleAndMenuShow) {
                this.$refs.menuBar.hideSetting()
            }
        },
        hideSettingAndMenu() {
            this.ifTitleAndMenuShow = false
        },
        setFontSize(fontSize) {
            this.defaultFontSize = fontSize
            if (this.themes) {
                this.themes.fontSize(this.defaultFontSize + 'px')
            }
        },
        registerTheme() {
            this.themeList.forEach(theme => {
                this.themes.register(theme.name, theme.style)
            })
        },
        setTheme(index) {
            this.defaultTheme = index
            this.themes.select(this.themeList[this.defaultTheme].name)
        },
        onProgressChange(progress) {
            const percentage = progress / 100
            const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
            this.rendition.display(location)
        },
        navTo(href) {
            this.rendition.display(href)
        }
    },
    components: {
        TitleBar: () => import('./TitleBar'),
        MenuBar: () => import('./MenuBar')
    }
}
</script>

<style lang="scss" scoped>
@import "src/assets/styles/globe.scss";
.ebook {
    position: relative;
    .mask{
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        display: flex;
        z-index: 99;

        .left{
            flex: 0 0 px2rem(100);
        }

        .mid{
            flex: 1;
        }

        .right{
            flex: 0 0 px2rem(100);
        }
    }
}
</style>
