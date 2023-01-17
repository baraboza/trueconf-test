<template>
  <div :class="$style.LiftShaft">
    <div
      :class="[$style.lift, {
        [$style._waiting]: lift.waiting
      }]"
      :style="{
        height,
        bottom: bottomPosition,
        transitionDuration,
      }"
    >
      <span
        v-show="lift.moving || lift.waiting"
        :class="$style.board"
        v-text="boardText"
      />
    </div>
  </div>
</template>

<script>
const ONE_FLOOR_SPEED = 1000
const WAITING_DURATION = 3000

export default {
  name: 'LiftShaft',

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
      transitionDuration: '',
      direction: ''
    }
  },

  computed: {
    bottomPosition () {
      return `${100 * (this.lift.floor - 1) / this.floorsNumber}%`
    },

    boardText () {
      const arrow = {
        up: '↑',
        down: '↓'
      }[this.direction]

      return `${arrow} ${this.lift.floor}`
    }
  },

  watch: {
    'lift.floor' (newValue, prevValue) {
      const movingDuration = ONE_FLOOR_SPEED * Math.abs(newValue - prevValue)
      this.transitionDuration = `${movingDuration}ms`

      this.direction = (newValue - prevValue) > 0 ? 'up' : 'down'

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
.LiftShaft {
  position: relative;
  width: 120px;
  border-left: 2px solid gray;
  border-right: 2px solid gray;
}

.lift {
  position: absolute;
  left: 0;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding-top: 10px;
  width: 100%;
  background: #66c9fe;
  transition-property: bottom;
  transition-timing-function: linear;

  &._waiting {
    animation: liftWaiting 0.8s infinite;
  }
}

.board {
  padding: 5px;
  width: 60px;
  text-align: center;
  border-radius: 3px;
  color: #fff;
  background-color: rgba(#000, 0.6);
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
