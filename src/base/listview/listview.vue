<template>
  <scroll class="listview" :data="data" ref="listview">
      <ul>
          <li v-for="(group,index) in data" class="list-group" :key="index" ref="listGroup">
              <h2 class="list-group-title">{{group.title}}</h2>
              <ul>
                 <li v-for="(item,index) in group.items" class="list-group-item" :key="index">
                     <img v-lazy="item.avatar" alt="头像" class="avatar">
                     <span class="name">{{item.name}}</span>
                 </li>
              </ul>
          </li>
      </ul>
      <div class="list-shortcut" @touchstart="onShortcutTouchStart" @touchmove.stop.prevent="onShortcutTouchMove">
        <ul>
          <li v-for="(item,index) in shortcutList" class="item" :key="index" :data-index="index">
            {{item}}
          </li>
        </ul>
      </div>
  </scroll>
</template>

<script>
import Scroll from 'base/scroll/scroll'
import {getData} from 'common/js/dom'

const ANCHOR_HEIGHT = 18

export default {
  created () {
    this.touch = {}
  },
  props: {
    data: {
      return: {
        type: Array,
        default: []
      }
    }
  },
  computed: {
    // 计算得到一个只有一个字母的title数组
    shortcutList () {
      return this.data.map((group) => {
        return group.title.substr(0, 1)
      })
    }
  },
  components: {
    Scroll
  },
  methods: {
    onShortcutTouchStart (e) {
      // 滚动到指定dom位置
      // 获取指定e的index
      let anchorIndex = getData(e.target, 'index')
      // 获取第一个手指touch的位置
      let firstTouch = e.touches[0]
      this.touch.y1 = firstTouch.pageY
      this.touch.anchorIndex = anchorIndex
      this._scrollTo(anchorIndex)
    },
    onShortcutTouchMove (e) {
      let firstTouch = e.touches[0]
      this.touch.y2 = firstTouch.pageY
      // 计算两次pageY的差值，即偏移量，再除以每个AHCHO_HEIGHT,得出便宜块数，|0用于向下取整
      let delta = (this.touch.y2 - this.touch.y1) / ANCHOR_HEIGHT | 0
      let anchorIndex = this.touch.anchorIndex + delta
      this._scrollTo(anchorIndex)
    },
    _scrollTo (index) {
      this.$refs.listview.scrollToElement(this.$refs.listGroup[index], arguments)
    }
  }
}

</script>
<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .listview
    position: relative
    width: 100%
    height: 100%
    overflow: hidden
    background: $color-background
    .list-group
      padding-bottom: 30px
      .list-group-title
        height: 30px
        line-height: 30px
        padding-left: 20px
        font-size: $font-size-small
        color: $color-text-l
        background: $color-highlight-background
      .list-group-item
        display: flex
        align-items: center
        padding: 20px 0 0 30px
        .avatar
          width: 50px
          height: 50px
          border-radius: 50%
        .name
          margin-left: 20px
          color: $color-text-l
          font-size: $font-size-medium
    .list-shortcut
      position: absolute
      z-index: 30
      right: 0
      top: 50%
      transform: translateY(-50%)
      width: 20px
      padding: 20px 0
      border-radius: 10px
      text-align: center
      background: $color-background-d
      font-family: Helvetica
      .item
        padding: 3px
        line-height: 1
        color: $color-text-l
        font-size: $font-size-small
        &.current
          color: $color-theme
    .list-fixed
      position: absolute
      top: 0
      left: 0
      width: 100%
      .fixed-title
        height: 30px
        line-height: 30px
        padding-left: 20px
        font-size: $font-size-small
        color: $color-text-l
        background: $color-highlight-background
    .loading-container
      position: absolute
      width: 100%
      top: 50%
      transform: translateY(-50%)
</style>