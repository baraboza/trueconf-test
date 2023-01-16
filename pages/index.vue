<template>
  <div :class="$style.IndexPage">
    <div :class="$style.wrap">
      <TheFloors
        :floors="floors"
        @button-click="onFloorButtonClick"
      />

      <TheShaft
        :floors-number="floors.length"
        :lifts="lifts"
        @lift-move-end="onLiftMoveEnd"
        @lift-wait-end="onLiftWaitEnd"
      />
    </div>
  </div>
</template>

<script>
import TheFloors from '@/components/TheFloors'
import TheShaft from '@/components/TheShaft'

const FLOORS_NUMBER = 5
const LIFTS_NUMBER = 1

export default {
  name: 'IndexPage',

  components: {
    TheFloors,
    TheShaft
  },

  data () {
    return {
      callQueue: [],
      lifts: []
    }
  },

  computed: {
    floors () {
      const floors = []

      for (let i = 0; i < FLOORS_NUMBER; i++) {
        const number = i + 1

        floors[i] = {
          number,
          waiting: (
            this.callQueue.includes(number) ||
            this.lifts.find(lift => lift.floor === number && lift.moving)
          )
        }
      }

      return floors
    }
  },

  watch: {
    callQueue () {
      if (!this.callQueue.length) { return }

      const floorInProgress = this.callQueue[0]

      const freeLift = this.lifts
        .filter(lift => !lift.moving && !lift.waiting)
        .sort((a, b) => Math.abs(a.floor - floorInProgress) - Math.abs(b.floor - floorInProgress))[0]

      if (!freeLift) { return }

      const freeLiftIndex = this.lifts.indexOf(freeLift)

      this.sendLift(freeLiftIndex)
    }
  },

  created () {
    this.initLifts()
  },

  methods: {
    initLifts () {
      for (let i = 0; i < LIFTS_NUMBER; i++) {
        this.lifts.push({
          floor: 1,
          moving: false,
          waiting: false
        })
      }
    },

    onFloorButtonClick (floorNumber) {
      if (this.callQueue.includes(floorNumber) || this.lifts.find(lift => lift.floor === floorNumber)) {
        return
      }

      this.callQueue.push(floorNumber)
    },

    onLiftMoveEnd (index) {
      const lift = this.lifts[index]

      lift.moving = false
      lift.waiting = true
    },

    onLiftWaitEnd (index) {
      const lift = this.lifts[index]

      lift.waiting = false
      this.sendLift(index)
    },

    sendLift (index) {
      if (!this.callQueue.length) { return }

      const lift = this.lifts[index]

      lift.moving = true
      lift.floor = this.callQueue.shift()
    }
  }
}
</script>

<style lang="scss" module>
.IndexPage {
  background: #e9f8fd;
  padding: 10px;
  height: 100vh;
}

.wrap {
  position: relative;
  height: 100%;
  border-top: 1px solid lightgray;
}
</style>
