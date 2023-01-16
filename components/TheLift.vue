<template>
  <div
    :class="[$style.TheLift, {
      [$style._waiting]: lift.waiting
    }]"
    :style="{
      height,
      bottom: bottomPosition,
      transitionDuration,
    }"
  />
</template>

<script>
const ONE_FLOOR_SPEED = 1000
const WAITING_DURATION = 3000

export default {
  name: 'TheLift',

  props: {
    height: {
      type: String,
      default: ''
    },

    lift: {
      type: Object,
      default: () => ({})
    },

    floorsNumber: {
      type: Number,
      default: 1
    }
  },

  data () {
    return {
      transitionDuration: ''
    }
  },

  computed: {
    bottomPosition () {
      return `${100 * (this.lift.floor - 1) / this.floorsNumber}%`
    }
  },

  watch: {
    'lift.floor' (newValue, prevValue) {
      const movingDuration = ONE_FLOOR_SPEED * Math.abs(newValue - prevValue)
      this.transitionDuration = `${movingDuration}ms`

      setTimeout(() => {
        this.$emit('move-end')

        setTimeout(() => {
          this.$emit('wait-end')
        }, WAITING_DURATION)
      }, movingDuration)
    }
  }
}
</script>

<style lang="scss" module>
.TheLift {
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  background: #66c9fe;
  transition-property: bottom;
  transition-timing-function: linear;

  &._waiting {
    animation: liftWaiting 0.8s infinite;
  }
}

@keyframes liftWaiting {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
  100% {
    opacity: 1;
  }
}
</style>
