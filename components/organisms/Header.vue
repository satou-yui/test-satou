<template lang="pug">
  header(:class="$style.header")
    div(:class="$style.inner")
      //- div(:style="{width: loadWidth + '%'}" :class="[$style.loadingbar, {[$style.transition]: transition}]")
      div(:class="[$style.toTop, {[$style.hover]: hover}]" @mouseover="hoverToTop", @mouseleave="hover = false" @click="toTop")
      h1(:class="$style.title")
        router-link(to="/") title
</template>

<script>
export default {
  data () {
    return {
      hover: false
    }
  },
  methods: {
    hoverToTop () {
      if (window.pageYOffset > 200) { this.hover = true }
    },
    toTop () {
      if (this.hover) {
        this.hover = false
        this.smoothScrollTop()
      }
    },
    smoothScrollTop () {
      let position = 0
      let progress = 0
      const easeOut = function (p) {
        return p * (2 - p)
      }

      const move = function () {
        progress++
        position = window.pageYOffset * easeOut(progress / 200)

        window.scrollTo(0, window.pageYOffset - position)

        if (position < window.pageYOffset) {
          requestAnimationFrame(move)
        }
      }

      requestAnimationFrame(move)
    }
  }
}
</script>

<style lang="stylus" module>
  .header
    width 100%
    height 65px
    background-color rgba(255, 255, 255, 0.9)
    position fixed
    top 0
    left 0
    z-index 100
    transition 0.5s

  .inner
    width 90%
    margin 0 auto

  .title
    text-align left
    height 45px
    width 100px
    line-height 55px
    a
      font-size(24px)
      font-family 'Rajdhani', sans-serif
      text-decoration none
      line-height 65px
      font-weight 600

  .toTop
    z-index 1000
    position fixed
    width calc(90% - 125px)
    height 65px
    top 0
    left calc(5% + 100px)
    &:after
      transform translateY(-100%)
      content 'BACK TO TOP'
      position fixed
      top 0
      left 0
      width 100%
      height 65px
      line-height 65px
      background #000
      text-align center
      color #fff
      font-size(14px)
      font-weight 600
      transition 0.5s

  .toTop.hover
    cursor pointer
    &:after
      transform translateY(0)
</style>
