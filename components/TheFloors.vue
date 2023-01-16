<template>
  <div :class="$style.TheFloors">
    <TheFloor
      v-for="floor in floors"
      :key="floor.number"
      :floor="floor"
      @button-click="$emit('button-click', $event)"
    />
  </div>
</template>

<script>
import TheFloor from '@/components/TheFloor'

export default {
  name: 'TheFloors',

  components: {
    TheFloor
  },

  props: {
    floors: {
      type: Array,
      default: () => []
    },

    lifts: {
      type: Array,
      default: () => []
    }
  }
}
</script>

<style lang="scss" module>
.TheFloors {
  display: flex;
  flex-direction: column-reverse;
  height: 100%;
}
</style>
