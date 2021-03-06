<template>
  <div class="md-ripple" @touchstart.passive.stop="startRipple" @mousedown.passive.stop="startRipple">
    <transition name="md-ripple" appear @after-enter="clearWave" v-if="!mdDisabled">
      <span class="md-ripple-wave" :class="rippleClass" :style="waveStyles" v-if="animating" ref="rippleWave" />
    </transition>

    <slot />
  </div>
</template>

<style lang="scss">
  @import '~components/MdAnimation/variables';

  .md-ripple {
    width: 100%;
    height: 100%;
    position: relative;
    z-index: 99;
    overflow: hidden;
    -webkit-mask-image: radial-gradient(circle, #fff 100%, #000 100%);
    transition: background-color $md-transition-default;
  }

  .md-ripple-wave {
    position: absolute;
    z-index: 1;
    pointer-events: none;
    background: currentColor;
    border-radius: 50%;
    opacity: 0;
    transform: scale(2) translateZ(0);

    &.md-centered {
      top: 50%;
      left: 50%;
    }

    ~ * {
      position: relative;
      z-index: 2;
    }
  }

  .md-ripple-enter-active {
    transition: 1s $md-transition-stand-timing;
    transition-property: opacity, transform;
    will-change: opacity, transform;

    &.md-centered {
      transition-duration: 1.2s;
    }
  }

  .md-ripple-enter {
    opacity: .26;
    transform: scale(0) translateZ(0);
  }
</style>

<script>
  import MdComponent from 'core/MdComponent'

  export default new MdComponent({
    name: 'MdRipple',
    props: {
      mdDisabled: Boolean,
      mdCentered: Boolean
    },
    data: () => ({
      eventType: null,
      waveStyles: null,
      animating: false
    }),
    computed: {
      rippleClass () {
        return {
          'md-centered': this.mdCentered
        }
      }
    },
    methods: {
      async startRipple ($event) {
        const { eventType, mdDisabled, mdCentered } = this

        if (!mdDisabled && (!eventType || eventType === $event.type)) {
          let rippleSize = this.getSize()
          let ripplePosition = null

          if (mdCentered) {
            ripplePosition = this.getCenteredPosition(rippleSize)
          } else {
            ripplePosition = this.getHitPosition($event, rippleSize)
          }

          await this.clearWave()

          this.eventType = $event.type
          this.animating = true
          this.applyStyles(ripplePosition, rippleSize)
        }
      },
      applyStyles (position, size) {
        size += 'px'

        this.waveStyles = {
          ...position,
          width: size,
          height: size
        }
      },
      clearWave () {
        this.waveStyles = null
        this.animating = false

        return this.$nextTick()
      },
      getSize () {
        const { offsetWidth, offsetHeight } = this.$el

        return Math.round(Math.max(offsetWidth, offsetHeight))
      },
      getCenteredPosition (size) {
        const halfSize = -size / 2 + 'px'

        return {
          'margin-top': halfSize,
          'margin-left': halfSize
        }
      },
      getHitPosition ($event, elementSize) {
        const rect = this.$el.getBoundingClientRect()
        let top = $event.pageY
        let left = $event.pageX

        if ($event.type === 'touchstart') {
          top = $event.changedTouches[0].pageY
          left = $event.changedTouches[0].pageX
        }

        return {
          top: top - rect.top - elementSize / 2 - document.body.scrollTop + 'px',
          left: left - rect.left - elementSize / 2 - document.body.scrollLeft + 'px'
        }
      }
    }
  })
</script>
