<template>
  <transition name="fade">
    <div class="window" v-show="showflag">
      <div class="title">
        <img src="../../assets/img/error.png" />
        <span>{{stoTypeName}}异常</span>
      </div>
      <div class="window-box">
        <ul class="table-title">
          <li class="index">序号</li>
          <li class="code">条码号</li>
          <li class="style">款号</li>
          <li class="color">颜色</li>
          <li class="size">尺码</li>
          <li class="reason">异常原因</li>
        </ul>
        <div class="table-data">
          <swiper class="swiper"
                  ref="mySwiper"
                  :options="swiperOption">
            <div class="swiper-slide"  v-for="(rows, index) in viewlist" :key="index">
              <ul class="row"
                  v-for="(row, idx) in rows"
                  :key="idx">
                <li class="index">{{row.index}}</li>
                <li class="code">{{row.BarCode}}</li>
                <li class="style">{{row.StyleName}}</li>
                <li class="color">{{row.ColorName}}</li>
                <li class="size">{{row.SizeName}}</li>
                <li class="reason" :style="{color: COLORS[row.DataStatus]}">{{row.DataStatusName}}</li>
              </ul>
            </div>
          </swiper>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  import { Swiper } from 'vue-awesome-swiper'
  import 'swiper/css/swiper.css'
  import { mapState } from 'vuex'
  const COLORS = {
    1: '#ff0000',
    3: '#ffff00',
    4: '#00ff00',
    5: '#00ffff'
  }
  export default {
    name: 'error-window',
    components: {
      Swiper
    },
    computed: {
      ...mapState([
        'stoTypeName',
        'windowMsg'
      ]),
      list() {
        return this.windowMsg.AbnormalDetailList ? this.windowMsg.AbnormalDetailList : []
      },
      viewlist() {
        let list = this.list.map((data, idx) => ({
          index: idx + 1,
          ...data
        }))
        return Array.from({length: Math.ceil(list.length / 6)}, () => ({})).map((_, index) => {
          let slice = list.slice(index * 6, (index + 1) * 6)
          return slice.length === 6 ? slice : slice.concat(Array.from({length: 6 - slice.length}, () => ({})))
        })
      },
      swiperOption() {
        return {
          initialSlide: 0,
          speed: 1000,
          autoplay: this.viewlist.length > 1 ? {
            delay: 3000,
            disableOnInteraction: false
          } : false,
          on: {
            reachEnd: () => {
              setTimeout(() => {
                this.swiper.destroy()
                this.reachEnd()
              }, 3000)
            }
          }
        }
      },
      swiper() {
        return this.$refs.mySwiper.swiperInstance
      }
    },
    data() {
      return {
        showflag: false,
        COLORS
      }
    },
    methods: {
      show() {
        this.showflag = true
      },
      hide() {
        this.showflag = false
      },
      reachEnd() {
        this.showflag = false
        this.$router.push('/outinstore')
        this.$store.commit('SET_WINDOWMSG', {})
      }
    },
    watch: {
      'viewlist'() {
        this.viewlist.length > 1 && this.swiper.autoplay.start()
      },
      'windowMsg.State'() {
        if (this.windowMsg.State === 'Abnormal') {
          this.showflag = true
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  @import '../../assets/stylus/scroll.styl'
  .window
    position: absolute
    width: 1920px
    height: 1080px
    background: rgba(0, 0, 0, .5)
    z-index: 100
    transition: all .3s

    &.fade-enter, &.fade-leave-to
      opacity: 0

    &.fade-enter-to, &.fade-leave
      opacity: 1
    .title
      color: #FF0000
      position: absolute
      top: 110px
      left: 300px
      img
        vertical-align: top
      span
        display: inline-block
        margin-left: 40px
        vertical-align: top
        height: 83px
        font-size: 87px
        font-weight: bold
    .window-box
      position: absolute
      top: 160px
      right: 0
      left: 0
      bottom: 0
      margin: auto
      width: 1375px
      height: 797px
      background-image: url("../../assets/img/error_windowbox.png")
      background-size: 1375px 797px
      background-repeat: no-repeat
      padding: 0 56px
      box-sizing: border-box
      .table-title
        display: flex
        height: 100px
        align-items: center
        text-align: center
        color: #94F0FD
        font-size: 27px
        margin-top: 20px
      .table-data
        height: 588px
        overflow: hidden
        .row
          display: flex
          text-align: center
          height: 98px
          align-items: center
          font-size: 28px
          &:nth-of-type(2n+1)
            background: rgba(14, 95, 183, .15)
          li
            overflow: hidden
            white-space: nowrap
            text-overflow: ellipsis
      .index
        flex: 0 0 80px
      .code
        flex: 0 0 200px
      .style
        flex: 0 0 233px
      .color
        flex: 0 0 350px
      .size
        flex: 0 0 130px
      .reason
        flex: 1
    .aaa
      color: #fff
</style>
