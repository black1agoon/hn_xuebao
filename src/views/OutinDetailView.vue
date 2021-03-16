<template>
  <div class="wrapper">
    <div class="detailbox">
      <div class="cnStatus">正在{{stoTypeName}}</div>
      <div class="enStatus">LOADING...</div>
      <div class="label">当前单据号:</div>
      <div class="no">123456789012</div>
      <div class="sum">
        <div class="text">第</div>
        <div class="number">{{stockList.length}}</div>
        <div class="text">件</div>
      </div>
    </div>
    <div class="skewbox">
      <img v-if="getPicUrl" :src="getPicUrl" />
    </div>
    <div class="picbox">
    </div>
    <div class="tablebox">
      <ul class="table-title">
          <li class="index">序号</li>
          <li class="code">条码号</li>
          <li class="style">款号</li>
          <li class="color">颜色</li>
          <li class="size">尺码</li>
      </ul>
      <div class="table-data">
        <swiper class="swiper"
                ref="mySwiper"
                :options="swiperOption">
          <div class="swiper-slide"
               v-for="(rows, index) in viewlist"
               :key="index">
            <ul class="row"
                v-for="(row, idx) in rows"
                :key="idx">
              <li class="index">{{row.index}}</li>
              <li class="code">{{row.colorDetail}}</li>
              <li class="style">{{row.factory_desc}}</li>
              <li class="color">{{row.ColorMain}}</li>
              <li class="size">{{row.size}}</li>
            </ul>
          </div>
        </swiper>

      </div>
    </div>
    <finish-window ref="finish">
    </finish-window>
    <error-window ref="error">
    </error-window>
  </div>
</template>

<script>
  import { Swiper } from 'vue-awesome-swiper'
  import 'swiper/css/swiper.css'
  import {mapState} from 'vuex'
  import FinishWindow from './window/FinishWindow'
  import ErrorWindow from './window/ErrorWindow'
  import {getImgUrl} from '../assets/js/utils'

  export default {
    name: 'outin-detail-view',
    components: {
      FinishWindow,
      ErrorWindow,
      Swiper
    },
    computed: {
      ...mapState([
        'stockList',
        'stoTypeName'
      ]),
      getPicUrl() {
        return this.stockList.length > 0 ? getImgUrl(this.stockList[0].PicName) : ''
      },
      viewlist() {
        let stockList = this.stockList.map((data, idx) => ({
          index: idx + 1,
          ...data
        }))
        return Array.from({length: Math.ceil(stockList.length / 15)}, () => ({})).map((_, index) => {
          let list = stockList.slice(index * 15, (index + 1) * 15)
          return list.length === 15 ? list : list.concat(Array.from({length: 15 - list.length}, () => ({})))
        })
      },
      swiperOption() {
        return {
          loop: true,
          speed: 1000,
          autoplay: this.viewlist.length > 1 ? {
            delay: 10000,
            disableOnInteraction: false
          } : false
        }
      },
      swiper() {
        return this.$refs.mySwiper.swiperInstance
      }
    },
    methods: {
    },
    watch: {
      'viewlist'() {
        this.viewlist.length > 1 && this.swiper.autoplay.start()
      },
      stockList: {
        immediate: true,
        handler: function () {
          this.$store.commit('SET_STOTYPENAME', this.stockList.length > 0 && this.stockList[0].StoType === 1 ? '入库' : '出库')
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
.wrapper
  position: absolute
  width: 1920px
  height: 1080px
  top: 0
  left: 0
  color: #ffffff
  .detailbox
    position: absolute
    z-index: 2
    top: 118px
    left: 28px
    width: 675px
    height: 214px
    background-image: url("../assets/img/detailbox.png")
    background-size: 675px 214px
    background-repeat: no-repeat
    .cnStatus
      position: absolute
      left: 184px
      top: 65px
      font-size: 34px
      font-weight: bold
    .enStatus
      position: absolute
      left: 184px
      top: 110px
      color: #A2D4F8
      font-size: 13px
      font-weight: bold
      letter-spacing: 2px
    .label
      position: absolute
      top: 170px
      left: 190px
      color: #DBF0FF
      font-size: 24px
      font-weight: bold
    .no
      position: absolute
      top: 166px
      left: 350px
      color: #DBF0FF
      font-size: 30px
      font-weight: bold
      letter-spacing: 2px
    .sum
      position: absolute
      display: flex
      top: 64px
      left: 418px
      height: 70px
      align-items: center
      .text
        flex: 0 0 60px
        text-align: center
        font-size: 24px
        color: #A2D4F8
      .number
        flex: 0 0 100px
        margin: 0 10px
        text-align: right
        color: #94F0FD
        font-size: 70px
        font-family: digital-dismay
  .picbox
    position: absolute
    top: 370px
    left: 92px
    width: 650px
    height: 650px
    background-image: url("../assets/img/picbox.png")
    background-size: cover
    overflow: hidden
    z-index: 20
  .skewbox
    position: absolute
    z-index: 10
    top: 269px
    left: -100px
    width: 950px
    height: 950px
    overflow: hidden
    transform: rotate(45deg)
    img
      width: 640px
      height: 640px
      margin: 90px 0 0 150px
      transform: rotate(-45deg)
  .tablebox
    position: absolute
    top: 91px
    left: 839px
    width: 1016px
    height: 923px
    background-image: url("../assets/img/tablebox.png")
    background-size: cover
    .table-title
      display: flex
      height: 98px
      align-items: center
      text-align: center
      color: #94F0FD
      font-size: 27px
    .table-data
      height: 825px
      overflow: hidden
      .row
        display: flex
        text-align: center
        height: 55px
        align-items: center
        font-size: 22px
        &:nth-of-type(2n + 1)
          background: rgba(14, 95, 183, .15)
    .index
      flex: 0 0 132px
    .code
      flex: 0 0 188px
    .style
      flex: 0 0 233px
    .color
      flex: 0 0 310px
    .size
      flex: 1
</style>
