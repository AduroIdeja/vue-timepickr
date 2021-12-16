<template>
  <div class="timepicker__active-bg" :style="transform"></div>
</template>

<script>
import store from '../store'

export default {
  data () {
    return store
  },
  computed: {
    transform () {
      const singleDigitWidth = 34
      const offset = this.activeIndex < 2
        ? this.activeIndex * singleDigitWidth
        : this.activeIndex * singleDigitWidth + singleDigitWidth

      return {
        transform: `translate(${offset}px, 0)`
      }
    }
  }
}
</script>
