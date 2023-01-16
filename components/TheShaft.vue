<template>
  <div :class="$style.TheShaft">
    <TheLift
      v-for="(lift, index) in lifts"
      :key="index"
      :lift="lift"
      :height="liftHeight"
      :floors-number="floorsNumber"
      @move-end="$emit('lift-move-end', index)"
      @wait-end="$emit('lift-wait-end', index)"
    />
  </div>
</template>

<script>
import TheLift from '@/components/TheLift'

export default {
  name: 'TheShaft',

  components: {
    TheLift
  },

  props: {
    floorsNumber: {
      type: Number,
      default: 1
    },

    lifts: {
      type: Array,
      default: () => []
    }
  },

  computed: {
    liftHeight () {
      return `${100 / this.floorsNumber}%`
    }
  }
}
</script>

<style lang="scss" module>
.TheShaft {
  position: absolute;
  top: 0;
  left: 10px;
  bottom: 0;
  width: 150px;
  border-left: 2px solid gray;
  border-right: 2px solid gray;
}
</style>
