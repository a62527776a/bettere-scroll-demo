<template lang="pug">
  .wrapper
    div
      .header
      .container
        .container-wrapper
          .list(v-for="(i, idx) in 4" :class="'list-' + idx" :key="'list-' + idx")
            div
              .list-item(v-for="i in 24")
</template>

<script>
import BScroll from 'better-scroll'

export default {
  name: 'app',
  data () {
    return {
      mainScroll: null,
      mainScrollActive: false,
      containerScroll: null,
      listItemScroll: {
        0: null,
        1: null,
        2: null,
        3: null
      },
      pageIdx: 0
    }
  },
  methods: {
    initMainScroll: function () {
      this.mainScroll = new BScroll('.wrapper', {
        bounce: false,
        eventPassthrough: 'horizontal'
      })
      this.mainScroll.disable()
      this.initContainerScroll()
    },
    initContainerScroll: function () {
      this.containerScroll = new BScroll('.container', {
        scrollX: true,
        snap: true,
        bounce: true,
        momentum: false,
        eventPassthrough: 'vertical'
      })
      this.containerScroll.on('scrollEnd', () => {
        this.pageIdx = this.containerScroll.getCurrentPage().pageX
        if (!this.listItemScroll[this.pageIdx]) this.initListItemScroll()
      })
      this.initListItemScroll()
    },
    initListItemScroll: function () {
      let el = document.querySelector('.list-' + this.pageIdx)
      this.listItemScroll[this.pageIdx] = new BScroll(el, {
        probeType: 3,
        eventPassthrough: 'horizontal',
        scrollY: true
      })
      this.listItemScroll[this.pageIdx].on('scroll', (e) => {
        if (this.listItemScroll[this.pageIdx].movingDirectionY === 1) {
          this.mainScroll.scrollToElement('.container-wrapper', 700, 0, 0)
          this.mainScrollActive = true
        }
        if (this.listItemScroll[this.pageIdx].movingDirectionY === -1 && e.y >= 0) {
          this.mainScroll.scrollTo(0, 0, 700)
          if (this.listItemScroll[this.pageIdx].isInTransition && this.mainScrollActive) {
            this.listItemScroll[this.pageIdx].scrollTo(0, 0)
          }
          this.mainScrollActive = false
        }
      })
    }
  },
  mounted () {
    this.initMainScroll()
  }
}
</script>

<style lang="sass">

@mixin rect($height, $color)
  margin: 1vh
  height: $height - 2vh
  width: calc(100% - 2vh)
  border-radius: 5px
  background: $color

body
  margin: 0

.wrapper
  height: 100vh
  overflow: hidden
  .header
    @include rect(30vh, red)
  .container-wrapper
    display: flex
    width: 400%
    overflow: hidden
  .container
    height: 100vh
    .list:nth-child(1)
      @include rect(100vh, green)
    .list:nth-child(2)
      @include rect(100vh, #6F865C)
    .list:nth-child(3)
      @include rect(100vh, blue)
    .list:nth-child(4)
      @include rect(100vh, black)
    .list-item
      @include rect(10vh, #E7E7E7)
</style>
