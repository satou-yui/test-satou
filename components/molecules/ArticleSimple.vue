<template lang="pug">
article(:class="$style.article" @click="link")
  figure(v-if="post.thumbnail")
    img(:src="post.thumbnail")
  figure(v-else :class="$style.noImage")
     span No Image
  div(:class="$style.body")
    h1 {{post.title}}
    div(:class="$style.text") {{post.description}}
    footer
      div(:class="$style.category") {{post.category.name}}
      div(:class="$style.tags")
        span(v-for = "tag in post.tags") {{tag.name}}
      div(:class="$style.data")
        span posted in
        time(:datetime = "post.date") {{post.created_at | formatDate}}
</template>

<script>
import moment from 'moment'

export default {
  components: {
  },
  props: {
    post: {
      type: Object,
      required: true
    }
  },
  methods: {
    link () {
      const m = moment(this.post.created_at, 'YYYYMMDD')
      const year = m.format('YYYY')
      const month = m.format('MM')
      const url = '/blog/' + year + '/' + month + '/' + this.post.slug

      this.$router.push(url)
    }
  }
}
</script>

<style lang="stylus" module>
.article
  cursor pointer
  display flex
  +breakpoint(small)
    margin 10px 0
  +breakpoint(middle)
    margin 15px 0
  &::before
    position absolute
    content 'Read\0020More'
    width 150px
    height 30px
    border solid 2px #fff
    opacity 0
    top 50%
    margin-top -15px
    left 50%
    margin-left -75px
    z-index 99
    text-align center
    font-size(14px)
    line-height 30px
    color #fff
    transition 0.5s
  &::after
    position absolute
    content ''
    width 0
    background-color #000
    opacity 0.6
    left 0
    +breakpoint(small)
      height 110px
      top 10px
    +breakpoint(middle)
      height 150px
      top 15px
    z-index 98
    transition 0.5s
  &:hover
    &::before
      opacity 1
    &::after
      width 100%
  figure
    img
      +breakpoint(small)
        width 110px
        height 110px
      +breakpoint(middle)
        width 150px
        height 150px
  .noImage
    display flex
    align-items center
    justify-content center
    background #000
    +breakpoint(small)
      width 110px
      height 110px
    +breakpoint(middle)
      width 150px
      height 150px
    span
      font-family 'Rajdhani', sans-serif
      font-size(18px)
      font-weight 600
      color #fff
  .body
    position relative
    +breakpoint(small)
      width calc(100% - 120px)
      margin-left 10px
    +breakpoint(middle)
      width calc(100% - 165px)
      margin-left 15px
    h1
      display -webkit-box
      overflow hidden
      -webkit-box-orient vertical
      max-width 100%
      line-height 15px
      height 15px
      overflow-wrap break-word
      -webkit-line-clamp 1
      +breakpoint(small)
        font-size(12px)
      +breakpoint(middle)
        font-size(14px)
    .text
      display -webkit-box
      overflow hidden
      -webkit-box-orient vertical
      max-width 100%
      font-size(11px)
      color #888
      overflow-wrap break-word
      margin 5px 0
      +breakpoint(small)
        max-height 30px
        -webkit-line-clamp 2
        line-height 15px /** 30 / 2 = 15**/
      +breakpoint(middle)
        max-height 90px
        -webkit-line-clamp 5
        line-height 15px /** 90 / 5 = 15**/
    footer
      position absolute
      bottom 0
      left 0
      .category
        &:before
          content: 'Category :';
          margin-right: 3px
      .tags
        &:before
          content: 'Tags :';
          margin-right: 3px
        span
          position relative
          &:not(:last-child)
            margin-right 15px
          &:not(:last-child):after
            position absolute
            content ''
            width 1px
            height 10px
            top 50%
            right -8px
            margin-top -5px
            -moz-transform rotate(20deg)
            -ms-transform rotate(20deg)
            -webkit-transform rotate(20deg)
            transform rotate(20deg)
            background-color #000
      .data
        color #aaa
        margin-top 5px
</style>
