<template>
  <div class="carousel-container">
    <div class="carousel-items-wrap">
      <slot></slot>
    </div>
    <div class="carousel-indicators" v-show="showIndicators">
      <div class="carousel-indicator" v-for="(page, $index) in pages" :key="$index" :class="{'active': index === $index}" @mouseover="goto($index)">
      </div>
    </div>
    <div class="carousel-btn carousel-btn-next" v-show="showSlideArrows" ref="arrowNext" @click="toNext" >
      <i class="iconfont icon-btn_right"></i>
    </div>
    <div class="carousel-btn carousel-btn-prev" v-show="showSlideArrows" ref="arrowPrev" @click="toPrev" >
      <i class="iconfont icon-btn_left"></i>
    </div>
  </div>
</template>

<script>
import {once} from 'wind-dom/src/event'
import {addClass, removeClass} from 'wind-dom/src/class'
export default {
  name: 'carousel',
  created () {
    this.dragState = {}
  },
  data () {
    return {
      ready: false,
      userScrolling: false,
      dragging: false,
      animating: false,
      noDrag: false,
      index: 0,
      pages: [],
      timer: null,
      initTimer: null
    }
  },
  props: {
    speed: {
      type: Number,
      default: 300
    },
    timeDelta: {
      type: Number,
      default: 3000
    },
    loop: {
      type: Boolean,
      default: true
    },
    noDragWhenSingle: {
      type: Boolean,
      default: true
    },
    showIndicators: {
      type: Boolean,
      default: true
    },
    showSlideArrows: {
      type: Boolean,
      default: true
    }
  },
  methods: {
    carouselItemCreatedOrDestroyed () {
      if (!this.ready) {
        return
      }
      clearTimeout(this.initTimer)
      this.initTimer = setTimeout(() => {
        this.initPages()
      }, 100)
    },
    initPages () {
      var children = this.$children
      this.noDrag = children.length === 1 && this.noDragWhenSingle
      var pages = []
      children.forEach((page, index) => {
        pages.push(page.$el)
        removeClass(page.$el, 'active')
        if (index === this.index) {
          addClass(page.$el, 'active')
        }
      })
      this.pages = pages
    },
    translate (element, offset, speed, callback) {
      if (!element) {
        return
      }
      if (speed) {
        this.animating = true
        element.style.transition = `transform ${speed}ms ease-in-out`
        element.style.transform = `translate3d(${offset}px, 0, 0)`
        var called = false
        var transitionEndCallback = () => {
          if (called) {
            return
          }
          called = true
          this.animating = false
          element.style.transition = ''
          element.style.transform = ''
          if (callback) {
            callback.apply(this, arguments)
          }
        }
        once(element, 'transitionEnd', transitionEndCallback)
        setTimeout(transitionEndCallback, speed + 100)
      } else {
        element.style.transition = ''
        element.style.transform = `translate3d(${offset}px, 0, 0)`
      }
    },
    doAnimate (towards, options) {
      if (this.$children.length === 0) {
        return
      }
      if (!options && this.$children.length < 2) {
        return
      }
      if (this.animating) {
        return
      }
      var prevPage, nextPage, currentPage, offsetWidth, offsetLeft
      var speed = this.speed || 300
      var pages = this.pages
      var index = this.index
      var pageCount = pages.length
      if (!options || towards === 'goto') {
        currentPage = pages[index]
        offsetWidth = this.$el.offsetWidth
        if (!options) {
          prevPage = pages[this.prevIndex(index)]
          nextPage = pages[this.nextIndex(index)]
        } else if (towards === 'goto') {
          prevPage = options.prevPage
          nextPage = options.nextPage
        }
        if (prevPage) {
          prevPage.style.display = 'block'
          this.translate(prevPage, -offsetWidth)
        }
        if (nextPage) {
          nextPage.style.display = 'block'
          this.translate(nextPage, offsetWidth)
        }
      } else {
        currentPage = options.currentPage
        prevPage = options.prevPage
        nextPage = options.nextPage
        offsetWidth = options.offsetWidth
        offsetLeft = options.offsetLeft
      }
      var newIndex
      var oldPage = this.$children[index].$el
      if (towards === 'prev') {
        newIndex = this.prevIndex(index)
      } else if (towards === 'next') {
        newIndex = this.nextIndex(index)
      } else if (towards === 'goto') {
        if (options.newIndex > -1 && options.newIndex < pageCount) {
          newIndex = options.newIndex
        }
      }
      var callback = () => {
        if (newIndex !== -1 && newIndex !== 'undefined') {
          var newPage = pages[newIndex]
          removeClass(oldPage, 'active')
          addClass(newPage, 'active')
          this.index = newIndex
          this.$emit('change', newIndex, index)
        }
        if (prevPage) {
          prevPage.style.display = ''
        }
        if (nextPage) {
          nextPage.style.display = ''
        }
      }
      if (newIndex === -1) {
        return
      }
      setTimeout(() => {
        if (towards === 'next') {
          this.translate(currentPage, -offsetWidth, speed, callback)
          if (nextPage) {
            this.translate(nextPage, 0, speed)
          }
        } else if (towards === 'prev') {
          this.translate(currentPage, offsetWidth, speed, callback)
          if (prevPage) {
            this.translate(prevPage, 0, speed)
          }
        } else if (towards === 'goto') {
          if (nextPage) {
            this.translate(currentPage, -offsetWidth, speed, callback)
            this.translate(nextPage, 0, speed)
          } else if (prevPage) {
            this.translate(currentPage, offsetWidth, speed, callback)
            this.translate(prevPage, 0, speed)
          }
        } else {
          this.translate(currentPage, 0, speed)
          if (offsetLeft !== 'undefined') {
            if (prevPage && offsetLeft > 0) {
              this.translate(prevPage, -offsetWidth, speed)
            }
            if (nextPage && offsetLeft < 0) {
              this.translate(nextPage, offsetWidth, speed)
            }
          } else {
            if (prevPage) {
              this.translate(prevPage, -offsetWidth, speed)
            }
            if (nextPage) {
              this.translate(nextPage, offsetWidth, speed)
            }
          }
        }
      })
    },
    prevIndex (index) {
      var pageCount = this.pages.length
      if (index === 0 && this.loop) {
        return pageCount - 1
      } else if (index === 0) {
        return -1
      } else {
        return index - 1
      }
    },
    nextIndex (index) {
      var pageCount = this.pages.length
      if (index === pageCount - 1 && this.loop) {
        return 0
      } else if (index === pageCount - 1) {
        return -1
      } else {
        return index + 1
      }
    },
    toPrev () {
      if (this.pages.length > 1) {
        if (!this.loop && this.index === 1) {
          removeClass(this.$refs.arrowPrev, 'active')
        }
        if (!this.loop && this.index < this.pages.length) {
          addClass(this.$refs.arrowNext, 'active')
        }
      }
      this.doAnimate('prev')
    },
    toNext () {
      if (this.pages.length > 1) {
        if (!this.loop && this.index === this.pages.length - 2) {
          removeClass(this.$refs.arrowNext, 'active')
        }
        if (!this.loop && this.index > -1) {
          addClass(this.$refs.arrowPrev, 'active')
        }
      }
      this.doAnimate('next')
    },
    goto (index) {
      if (index === this.index) {
        return
      }
      if (index < this.index) {
        this.doAnimate('goto', {
          newIndex: index,
          prevPage: this.pages[index]
        })
      } else {
        this.doAnimate('goto', {
          newIndex: index,
          nextPage: this.pages[index]
        })
      }
    },
    doOnStart (event) {
      if (this.noDrag) {
        return
      }
      var element = this.$el
      var dragState = this.dragState
      var touch = event.changedTouches ? event.changedTouches[0] : event
      dragState.startTime = new Date()
      dragState.startLeft = touch.pageX
      dragState.startTop = touch.pageY
      dragState.startTopAbsolute = touch.clientY
      dragState.offsetWidth = element.offsetWidth
      dragState.offsetHeight = element.offsetHeight
      var prevPage = this.$children[this.prevIndex(this.index)]
      var dragPage = this.$children[this.index]
      var nextPage = this.$children[this.nextIndex(this.index)]
      dragState.prevPage = prevPage ? prevPage.$el : null
      dragState.currentPage = dragPage ? dragPage.$el : null
      dragState.nextPage = nextPage ? nextPage.$el : null
      if (prevPage) {
        dragState.prevPage.style.display = 'block'
      }
      if (nextPage) {
        dragState.nextPage.style.display = 'block'
      }
      this.dragState = dragState
    },
    doOnMove (event) {
      if (this.noDrag) {
        return
      }
      var dragState = this.dragState
      var touch = event.changedTouches ? event.changedTouches[0] : event
      dragState.currentLeft = touch.pageX
      dragState.currentTop = touch.pageY
      dragState.currentTopAbsolute = touch.clientY
      var offsetLeft = dragState.currentLeft - dragState.startLeft
      var offsetTop = dragState.currentTop - dragState.startTop
      var distanceX = Math.abs(offsetLeft)
      var distanceY = Math.abs(offsetTop)
      if (distanceX < 5 || (distanceX >= 5 && distanceX * 1.73 <= distanceY)) {
        this.userScrolling = true
        return
      } else {
        this.userScrolling = false
        event.preventDefault()
      }
      offsetLeft = Math.min(Math.max(-dragState.offsetWidth + 1, offsetLeft), dragState.offsetWidth - 1)
      var towards = offsetLeft < 0 ? 'next' : 'prev'
      if (dragState.prevPage && towards === 'prev') {
        this.translate(dragState.prevPage, offsetLeft - dragState.offsetWidth)
      }
      this.translate(dragState.currentPage, offsetLeft)
      if (dragState.nextPage && towards === 'next') {
        this.translate(dragState.nextPage, offsetLeft + dragState.offsetWidth)
      }
      this.dragState = dragState
    },
    doOnEnd (event) {
      if (this.noDrag) {
        return
      }
      var dragState = this.dragState
      var dragDuration = new Date() - dragState.startTime
      var towards = null
      var offsetLeft = dragState.currentLeft - dragState.startLeft
      var offsetTop = dragState.currentTop - dragState.startTop
      var offsetWidth = dragState.offsetWidth
      var index = this.index
      var pageCount = this.pages.length
      if (dragDuration < this.timeDelta) {
        var fireTap = Math.abs(offsetLeft) < 5 && Math.abs(offsetTop) < 5
        if (isNaN(offsetLeft) || isNaN(offsetTop)) {
          fireTap = true
        }
        if (fireTap) {
          this.$children[this.index].$emit('tap')
        }
      }
      if (dragDuration < this.timeDelta && dragState.currentLeft === undefined) {
        return
      }
      if (dragDuration < this.timeDelta && Math.abs(offsetLeft) > offsetWidth / 2) {
        towards = offsetLeft < 0 ? 'next' : 'prev'
      }
      if (!this.loop) {
        if ((index === 0 && towards === 'prev') || (index === pageCount - 1 && towards === 'next')) {
          towards = null
        }
      }
      if (this.$children.length < 2) {
        towards = null
      }
      this.doAnimate(towards, {
        offsetLeft: offsetLeft,
        offsetWidth: dragState.offsetWidth,
        prevPage: dragState.prevPage,
        currentPage: dragState.currentPage,
        nextPage: dragState.nextPage
      })
      this.dragState = {}
    },
    dragStartEvent (event) {
      event.preventDefault()
      if (this.animating) {
        return
      }
      this.dragging = true
      this.userScrolling = false
      clearInterval(this.timer)
      this.doOnStart(event)
    },
    dragMoveEvent (event) {
      if (!this.dragging) {
        return
      }
      this.doOnMove(event)
    },
    dragEndEvent (event) {
      if (this.userScrolling) {
        this.dragging = false
        this.dragState = {}
        return
      }
      if (this.dragging) {
        this.doOnEnd(event)
        this.dragging = false
      }
      if (this.timeDelta > 0) {
        this.timer = setInterval(() => {
          this.toNext()
        }, this.timeDelta)
      }
    },
    mouseOverEvent (event) {
      if (this.pages.length > 1) {
        addClass(this.$refs.arrowNext, 'active')
        addClass(this.$refs.arrowPrev, 'active')
        if (!this.loop) {
          if (this.index === 0) {
            removeClass(this.$refs.arrowPrev, 'active')
          }
          if (this.index === this.pages.length - 1) {
            removeClass(this.$refs.arrowNext, 'active')
          }
        }
      }
    },
    mouseOutEvent (event) {
      if (this.pages.length > 1) {
        removeClass(this.$refs.arrowNext, 'active')
        removeClass(this.$refs.arrowPrev, 'active')
      }
    }
  },
  destroyed () {
    if (this.timer) {
      clearInterval(this.timer)
      this.timer = null
    }
    if (this.initTimer) {
      clearTimeout(this.initTimer)
      this.initTimer = null
    }
  },
  mounted () {
    this.ready = true
    this.initPages()
    if (this.timeDelta > 0) {
      this.timer = setInterval(() => {
        this.toNext()
      }, this.timeDelta)
    }
    this.initPages()
    var element = this.$el
    // for mobile
    element.addEventListener('touchstart', this.dragStartEvent)
    element.addEventListener('touchmove', this.dragMoveEvent)
    element.addEventListener('touchend', this.dragEndEvent)
    // for pc
    element.addEventListener('mouseover', this.mouseOverEvent)
    element.addEventListener('mouseout', this.mouseOutEvent)
  }
}
</script>

<style scoped>
@import "carousel.less";
.carousel-container,.carousel-items-wrap{
  position: relative;
  overflow: hidden;
}
.carousel-items-wrap{
  height: 100%;
  transform: translateZ(0);
}
.carousel-items-wrap > div {
  position: absolute;
  width: 100%;
  height: 100%;
  transform: translateX(-100%);
  display: none;
}
.carousel-items-wrap > div.active {
  transform: none;
  display: block
}
</style>
