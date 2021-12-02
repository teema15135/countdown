<template>
  <div class="container">
    <h1 class="title">Arrive Khon Kaen in</h1>
    <div v-if="showTime" class="time-text">
      <div v-if="time.day != 0" class="time-text-container">
        <span class="day time">{{ time.day }}</span>
        <span class="day label">Day{{ time.day > 1 ? 's' : '' }}</span>
      </div>
      <div v-if="time.hour != 0" class="time-text-container">
        <span class="hour time">{{ time.hour }}</span>
        <span class="hour label">Hour{{ time.hour > 1 ? 's' : '' }}</span>
      </div>
      <div v-if="time.minute != 0" class="time-text-container">
        <span class="minute time">{{ time.minute }}</span>
        <span class="minute label">Minute{{ time.minute > 1 ? 's' : '' }}</span>
      </div>
      <div class="time-text-container">
        <span class="second time">{{ time.second }}</span>
        <span class="second label">Second{{ time.second > 1 ? 's' : '' }}</span>
      </div>
    </div>
    <div v-if="!showTime" class="time-complete">COMPLETE!!</div>
    <EditIcon v-if="!showDateTimePicker" id="edit-icon" class="mt-4" v-on:click.native="toggleShowDateTimePicker()" />
    <div v-if="showDateTimePicker" class="mt-4 date-picker-container flex flex-row">
      <input v-model="dateTimePickerValue" type="datetime-local" />
      <CheckIcon id="edit-icon" v-on:click.native="toggleShowDateTimePicker()" />
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

import EditIcon from '@/components/EditIcon.vue'
import CheckIcon from '@/components/CheckIcon.vue'
const TIME_LOCAL_STORAGE_KEY = 'countDownTime'

export default Vue.extend({
  components: {
    EditIcon,
    CheckIcon,
  },
  data() {
    return {
      showTime: true,
      showDateTimePicker: false,
      countTo: Date.now(),
      dateTimePickerValue: '',
      time: {
        day: 0,
        hour: 0,
        minute: 0,
        second: 0
      },
      tick: true
    }
  },
  mounted() {
    this.dateTimePickerValue = this.getDateTimePickerFromLocalStorage()
    setInterval(() => {
      this.tick = !this.tick
    }, 100)
  },
  methods: {
    updateTimeFromDateTimePickerValue() {
      let timeLeft = this.getTimeLeft()
      if (timeLeft == false) this.showTime = false
      else {
        this.time = {
          day: timeLeft.day,
          hour: timeLeft.hour,
          minute: timeLeft.minute,
          second: timeLeft.second
        }
        this.showTime = true
      }
    },
    toggleShowDateTimePicker() {
      this.showDateTimePicker = !this.showDateTimePicker
    },
    getTimeLeft() {
      let left = Math.floor((this.countTo - Date.now()) / 1000)
      if (left < 0) return false
      let second = left % 60
      let minute = Math.floor(left / 60) % 60
      let hour = Math.floor(left / 3600) % 24
      let day = Math.floor(left / 86400)
      return {
        day,
        hour,
        minute,
        second
      }
    },
    getDateTimePickerFromLocalStorage(): string {
      return localStorage.getItem(TIME_LOCAL_STORAGE_KEY) || ''
    }
  },
  watch: {
    tick(value) {
      this.updateTimeFromDateTimePickerValue()
    },
    dateTimePickerValue(value) {
      if (value.trim().length !== 0) {
        localStorage.setItem(TIME_LOCAL_STORAGE_KEY, value)
        this.countTo = new Date(value).getTime()
        this.updateTimeFromDateTimePickerValue()
      }
    }
  }
})
</script>

<style>
html {
  @apply bg-yellowGreenCrayola;
}

.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  font-family: 'Source Sans Pro', -apple-system, BlinkMacSystemFont, 'Segoe UI',
    Roboto, 'Helvetica Neue', Arial, sans-serif;
}

#edit-icon {
  @apply text-fernGreen w-6 h-6
}

.title {
  @apply text-britishRacingGreen text-4xl;
}

.time-text {
  @apply flex flex-row flex-wrap text-center justify-center text-raisinBlack;
  font-size: 24pt;
}

.time-text-container {
  margin-right: 16px;
}

.time-text .label {
  @apply text-fernGreen;
  font-size: 36pt;
}

.time-text .time {
  @apply text-britishRacingGreen;
  font-size: 48pt;
}

.time-complete {
  @apply text-fernGreen;
  font-size: 48pt;
}
</style>
