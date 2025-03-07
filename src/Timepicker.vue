<template>
  <div class="timepicker-wrap">
    <svg class="timepicker-icon timepicker-icon__clock" viewBox="0 0 32 32">
      <path class="path1" d="M20.586 23.414l-6.586-6.586v-8.828h4v7.172l5.414 5.414zM16 0c-8.837 0-16 7.163-16 16s7.163 16 16 16 16-7.163 16-16-7.163-16-16-16zM16 28c-6.627 0-12-5.373-12-12s5.373-12 12-12c6.627 0 12 5.373 12 12s-5.373 12-12 12z"></path>
    </svg>
    <input type="text" class="time" ref="timeInput" :value="val" @focus="open">
    <div class="timepicker" tabindex="0" :class="{'is-open': isOpen}" ref="timepicker">
      <div class="timepicker__header">
        Set time
      </div>
      <div class="timepicker__time">
        <time-unit :value="time[0]" index="0"></time-unit>
        <time-unit :value="time[1]" index="1"></time-unit>
        <div class="timepicker__separator">:</div>
        <time-unit :value="time[2]" index="2"></time-unit>
        <time-unit :value="time[3]" index="3"></time-unit>
        <active-background></active-background>
      </div>
      <numpad :on-close="close"></numpad>
    </div>
  </div>
</template>

<script>
import store from './store'
import { getDigit, filteredDigits } from './helpers'

import KeyboardEvents from './mixins/KeyboardEvents'
import CommonActions from './mixins/CommonActions'
import ActiveBackground from './components/ActiveBackground'
import TimeUnit from './components/TimeUnit'
import Numpad from './components/Numpad'

export default {
  props: ['value'],
  mixins: [KeyboardEvents, CommonActions],
  components: {
    ActiveBackground,
    TimeUnit,
    Numpad
  },
  data () {
    return store
  },
  created () {
    this.time = this.value.replace(':', '').split('')
  },
  computed: {
    filteredDigits () {
      return filteredDigits(this.activeIndex, this.digits, this.time)
    },
    val: {
      get () {
        return this.value
      },
      set (val) {
        this.$emit('input', val)
      }
    }
  },
  methods: {
    open () {
      this.activeIndex = 0
      this.$refs.timeInput.blur()
      this.$refs.timepicker.focus()
      this.$nextTick(() => {
        this.isOpen = true
      })
    },
    close () {
      this.setTime()
      this.isOpen = false
    },
    setTime () {
      this.val = `${this.time[0]}${this.time[1]}:${this.time[2]}${this.time[3]}`
    },
    digitPressed (digit) {
      const pressedDigit = getDigit(this.digits, digit)
      pressedDigit.pressed = true
    },
    arrowPressed (direction) {
      this.arrowKeys[direction].pressed = true
    }
  }
}
</script>

<style lang="scss">
$header-bg: #f25f5c;
$time-bg: lighten($header-bg, 10%);
$active-unit-bg: $header-bg;
$digit-color: #757575;
$border-radius: 3px;
$input-width: 34px;

.timepicker-wrap {
  display: inline-block;
  position: relative;

  *,
  *::after,
  *::before {
    box-sizing: border-box;
  }
}

.time {
  border: 1px solid rgba(0, 0, 0, 0.1);
  padding: 15px 10px 15px 40px;
  border-radius: 3px;
  font-size: 1.2rem;
  width: 120px;
  text-align: center;
}

.timepicker-icon {
  position: absolute;
  left: 15px;
  top: 50%;
  display: inline-block;
  width: 1em;
  height: 1em;
  stroke-width: 0;
  stroke: currentColor;
  fill: currentColor;
  transform: translate(0, -50%);
}

.timepicker {
  position: absolute;
  background: #fbfbff;
  width: 250px;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.3);
  border-radius: $border-radius;
  overflow: hidden;
  left: 50%;
  top: -135px;
  border-radius: 50%;
  transition: all 0.3s ease;
  transform: translate(-50%, 0) scale(0);
  pointer-events: none;

  &.is-open {
    transform: translate(-50%, 0) scale(1);
    border-radius: $border-radius;
    pointer-events: auto;
  }

  &__header {
    padding: 5px 15px;
    font-size: 14px;
    color: #fff;
    background: $header-bg;
  }
  &__time {
    position: relative;
    background: $time-bg;
    display: flex;
    justify-content: center;
    font-size: 50px;
    color: #fff;
    line-height: 1;
    padding: 12px 10px;
  }

  &__active-bg {
    position: absolute;
    top: 0;
    left: 40px;
    width: $input-width;
    height: 100%;
    background: $active-unit-bg;
    transition: transform 0.4s ease;
  }

  &__separator {
    z-index: 2;
    position: relative;
    width: $input-width;
    text-align: center;
    font-size: 90%;
  }
}
</style>
