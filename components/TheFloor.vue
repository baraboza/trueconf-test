<template>
  <div :class="$style.TheFloor">
    <p :class="$style.number">
      {{ floor.number }}
    </p>

    <button
      type="button"
      :class="[$style.button, {
        [$style._active]: floor.waiting
      }]"
      @click="$emit('button-click', floor.number)"
    />
  </div>
</template>

<script>
export default {
  name: 'TheFloor',

  props: {
    floor: {
      type: Object,
      default: () => ({})
    }
  }
}
</script>

<style lang="scss" module>
.TheFloor {
  flex-grow: 1;
  border-bottom: 1px solid lightgray;
  padding: 10px;
}

.number {
  font-weight: 700;
}

.button {
  margin-top: 15px;
  width: 30px;
  height: 30px;
  background: #f3f6f7;
  border: 1px solid #559;
  border-radius: 2px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;

  &::before {
    content: "";
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #559;
    border: 3px solid #f3f6f7;
    box-shadow: 0 0 0 2px #559;
  }

  &._active {
    &::before {
      background: orange;
      box-shadow: 0 0 0 2px orange;
    }
  }
}
</style>
