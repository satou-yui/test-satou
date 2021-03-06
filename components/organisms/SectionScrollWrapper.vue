<template lang="pug">
  main(:class="$style.main")
    div(:class="[$style.wrapper, { [$style.initial]: initialLoadingFlag }]" :style="{transform: ' translate3d(0px,' + animationSectionIndex * windowHeight * -1 +'px, 0px)'}")
      slot
    SectionScrollNavigation(:class="$style.nav" :anchor-list="anchorList", :active-section-index="animationSectionIndex", :direction-row="queryMatches")
    SectionScrollCounter(:class="$style.counter" :active-section-index='animationSectionIndex', :section-number='anchorList.length - 1')
</template>

<script>
import _ from 'lodash'
import SectionScrollNavigation from '../molecules/SectionScrollNavication.vue'
import SectionScrollCounter from '../molecules/SectionScrollCounter.vue'

export default {
  components: {
    SectionScrollNavigation,
    SectionScrollCounter
  },
  props: {
    anchorList: {
      type: Array,
      required: true
    }
  },
  data () {
    return {
      mousewheelevent: '',
      activeSectionIndex: 0,
      animationSectionIndex: 0,
      windowHeight: '',
      prevTime: '',
      prevDelta: '',
      anchorListState: '',
      initialLoadingFlag: true,
      matchMedia: {},
      queryMatches: ''
    }
  },
  watch: {
    '$route' (to, from) {
      if (to.hash !== from.hash) {
        const self = this
        const toIndex = _.findIndex(self.anchorListState, function (anchor) { return anchor === to.hash })
        const fromIndex = _.findIndex(self.anchorListState, function (anchor) { return anchor === from.hash })
        this.activeSectionIndex = toIndex

        if (toIndex < fromIndex) {
          this.scrollUp(toIndex)
        } else {
          this.scrollDown(toIndex)
        }
      }
    },
    anchorList (value) {
      this.anchorListState = value

      const self = this
      const index = _.findIndex(self.anchorListState, function (anchor) { return anchor === self.$route.hash })
      this.activeSectionIndex = index
      this.animationSectionIndex = index
    },
    activeSectionIndex (index, prevIndex) {
      const toHash = this.anchorListState[index]
      if (toHash !== this.$route.hash && !this.initialLoadingFlag) {
        this.$router.push({ hash: toHash })
      }
    }
  },
  mounted () {
    this.mousewheelevent = 'onwheel' in document ? 'wheel' : 'onmousewheel' in document ? 'mousewheel' : 'DOMMouseScroll'
    this.windowHeight = 'innerHeight' in window ? window.innerHeight : document.documentElement.offsetHeight

    window.addEventListener('resize', this.handleResize)
    window.addEventListener(this.mousewheelevent, this.handleWheel, false)
    window.addEventListener('keydown', this.handleKeydown, false)

    this.prevTime = new Date().getTime()
    this.initialLoading()

    this.$nextTick(() => {
      const querySizes = [this.$style.small, this.$style.middle]

      querySizes.forEach((querySize, index) => {
        this.matchMedia[index] = window.matchMedia(querySize)
        this.matchMedia[index].addListener(this.checkNavigationRotation)
        this.checkNavigationRotation(this.matchMedia[index])
      })
    })
  },
  beforeDestroy () {
    window.removeEventListener('resize', this.handleResize)
    window.removeEventListener(this.mousewheelevent, this.handleWheel, false)
    window.removeEventListener('keydown', this.handleKeydown, false)
  },
  methods: {
    handleResize: _.debounce(function () {
      this.windowHeight = 'innerHeight' in window ? window.innerHeight : document.documentElement.offsetHeight
    }, 300),

    handleWheel (e) {
      let curTime
      let isFired = false

      const delta = Math.abs(e.deltaY ? -(e.deltaY) : e.wheelDelta ? e.wheelDelta : -(e.detail))

      if (!delta) { return }

      if (delta - this.prevDelta > 0) {
        curTime = e.timeStamp

        if (!isFired && curTime - this.prevTime > 250) {
          if (e.deltaY < 0 && this.activeSectionIndex > 0) {
            this.activeSectionIndex--
          } else if (this.activeSectionIndex < this.anchorListState.length - 1) {
            this.activeSectionIndex++
          }

          isFired = true
        }

        this.prevTime = curTime
      } else {
        isFired = false
      }

      this.prevDelta = delta
    },
    handleKeydown (e) {
      switch (e.key) {
        case 'ArrowUp':
          if (this.activeSectionIndex > 0) {
            this.activeSectionIndex--
          }
          break
        case 'ArrowDown':
          if (this.activeSectionIndex < this.anchorListState.length - 1) {
            this.activeSectionIndex++
          }
          break
        default:
          break
      }
    },
    async initialLoading () {
      await this.$delay(1000, () => {
        this.initialLoadingFlag = false
      }).exec()
    },
    async scrollUp (index) {
      if (this.animationSectionIndex > 0) {
        this.$emit('animationReverse', this.animationSectionIndex)
        if (index) {
          this.animationSectionIndex = index
        } else {
          this.animationSectionIndex--
        }

        await this.$delay(1200, () => {
          this.$emit('animationPlay', this.animationSectionIndex)
        }).exec()
      }
    },
    async scrollDown (index) {
      if (this.animationSectionIndex < this.anchorListState.length - 1) {
        this.$emit('animationReverse', this.animationSectionIndex)
        if (index) {
          this.animationSectionIndex = index
        } else {
          this.animationSectionIndex++
        }

        await this.$delay(1200, () => {
          this.$emit('animationPlay', this.animationSectionIndex)
        }).exec()
      }
    },
    checkNavigationRotation () {
      this.queryMatches = Object.entries(this.matchMedia)
        .map(([key, value]) => (value))
        .some(query => query.matches)
    }
  }
}
</script>

<style lang="stylus" module>
  .main
    position fixed
    top 0
    left 0
    height 100vh
    section
      height 100vh
      width 100vw

  .wrapper
    height 100vh
    touch-action none
    position relative
    transition all 700ms ease 1000ms
  .nav
    position fixed
    left 0
    bottom: 50%
    transform translateY(50%)
    +breakpoint(middle small)
      left auto
      right 15%
      bottom 0
      transform translateY(0)
    +breakpoint(small)
      right 20%
  .counter
    position fixed
    bottom 2.5%
    right 2.5%
  .initial
    transition none;
</style>
