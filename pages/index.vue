<template>
  <div :class="$style.IndexPage">
    <div :class="$style.wrap">
      <div :class="$style.floors">
        <TheFloor
          v-for="floor in floors"
          :key="floor.number"
          :floor="floor"
          :style="{paddingLeft: floorPaddingLeft}"
          @button-click="onFloorButtonClick"
        />
      </div>

      <div ref="shafts" :class="$style.shafts">
        <LiftShaft
          v-for="(lift, index) in lifts"
          :key="index"
          :class="$style.shaft"
          :lift="lift"
          :height="liftHeight"
          :floors-number="floors.length"
          @move-end="onLiftMoveEnd(index)"
          @wait-end="onLiftWaitEnd(index)"
        />
      </div>
    </div>
  </div>
</template>

<script>
import TheFloor from '@/components/TheFloor'
import LiftShaft from '@/components/LiftShaft'

const FLOORS_NUMBER = 7
const LIFTS_NUMBER = 4

export default {
  name: 'IndexPage',

  components: {
    TheFloor,
    LiftShaft
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
          waiting: Boolean(
            this.callQueue.includes(number) ||
            this.lifts.find(lift => lift.floor === number && lift.moving)
          )
        }
      }

      return floors
    },

    liftHeight () {
      return `${100 / this.floors.length}%`
    },

    floorPaddingLeft () {
      return `${this.lifts.length * 130 + 20}px`
    }
  },

  watch: {
    callQueue () {
      this.saveObjectInLS('callQueue', this.callQueue)

      if (!this.callQueue.length) { return }

      const floorInProgress = this.callQueue[0]

      const freeLifts = this.lifts.filter(lift => !lift.moving && !lift.waiting)

      let closestLift

      if (!freeLifts.length) {
        return
      } else if (freeLifts.length === 1) {
        closestLift = freeLifts[0]
      } else {
        closestLift = freeLifts.sort((a, b) => Math.abs(a.floor - floorInProgress) - Math.abs(b.floor - floorInProgress))[0]
      }

      this.sendLift(this.lifts.indexOf(closestLift))
    }
  },

  mounted () {
    this.initLifts()
    this.$nextTick(() => this.initCallQueue())
  },

  methods: {
    initLifts () {
      const liftsFromLS = this.getObjectFromLS('lifts') || []

      for (let i = 0; i < LIFTS_NUMBER; i++) {
        this.lifts.push(liftsFromLS[i] || {
          floor: 1,
          moving: false,
          waiting: false
        })
      }
    },

    initCallQueue () {
      this.callQueue = this.getObjectFromLS('callQueue') || []
    },

    getObjectFromLS (name) {
      const value = localStorage.getItem(name)

      if (value) {
        try {
          return JSON.parse(value)
        } catch (e) {
          localStorage.removeItem(name)
        }
      }
    },

    saveObjectInLS (name, value) {
      localStorage.setItem(name, JSON.stringify(value))
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

      this.saveObjectInLS('lifts', this.lifts.map(lift => ({
        ...lift,
        moving: false,
        waiting: false
      })))
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

.floors {
  display: flex;
  flex-direction: column-reverse;
  height: 100%;
}

.shafts {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  display: flex;
}

.shaft {
  margin-left: 10px;
}
</style>
