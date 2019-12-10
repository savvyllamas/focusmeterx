<template>
<section id="stopwatch">
<h3>{{title}}</h3>
<div id="timer">
  <span id="minutes">{{minutes}}</span>
  <span id="middle">:</span>
  <span id="seconds">{{seconds}}</span>
</div>
<div id="buttons">
  <button
    id="start"
    class="button is-dark is-large"
    v-if="!timer"
    @click="startTimer">
     Начать
  </button>
  <button
    id="stop"
    class="button is-dark is-large"
    v-if="timer"
    @click="stopTimer">
      Остановить
  </button>
  <button
    id="reset"
    class="button is-dark is-large"
    v-if="resetButton"
    @click="resetTimer">
      Заново
  </button>
</div>
</section>
</template>

<script>
export default {
  name: 'Stopwatch',
  props: ['state'],
  watch: {
    state: function (newState, oldState) {
      if (newState === 'stopped' && oldState === 'going') {
        console.log('external timer stop')
        this.stopTimerExternal()
      }
      if (newState === 'started') {
        this.startTimer()
        this.state = 'going'
      }
    }
  },
  data: function () {
    return {
      timer: null,
      timestamps: [],
      totalTime: 0,
      resetButton: false,
      title: 'Начать тестирование'
    }
  },
  methods: {
    created () {
      this.timestamps = []
    },
    startTimer: function () {
      this.timestamps.push({ start: Date.now() })
      this.timer = setInterval(() => this.countdown(), 1000)
      this.resetButton = true
      this.title = 'Идёт тестирование'
      this.$emit('testing-start', Date.now())
      // this.state = 'started'
    },
    stopTimerExternal: function () {
      this.timestamps.push({ stop: Date.now() })
      clearInterval(this.timer)
      this.timer = null
      this.resetButton = true
      this.title = 'Тестирование остановлено'
      // this.$emit('testing-stop', Date.now())
      // this.state = 'stopped'
    },
    stopTimer: function () {
      this.timestamps.push({ stop: Date.now() })
      clearInterval(this.timer)
      this.timer = null
      this.resetButton = true
      this.title = 'Тестирование остановлено'
      this.$emit('testing-stop', Date.now())
      // this.state = 'stopped'
    },
    resetTimer: function () {
      this.timestamps.push({ reset: Date.now() })
      this.totalTime = 0
      clearInterval(this.timer)
      this.timer = null
      this.resetButton = false
      this.title = 'Тестирование начато заново'
      this.$emit('testing-reset', Date.now())
      // this.state = 'reseted'
    },
    padTime: function (time) {
      return (time < 10 ? '0' : '') + time
    },
    countdown: function () {
      if (this.timer) {
        this.totalTime++
      } else {
        this.totalTime = 0
        this.resetTimer()
      }
    }
  },

  computed: {
    minutes: function () {
      const minutes = Math.floor(this.totalTime / 60)
      return this.padTime(minutes)
    },
    seconds: function () {
      const seconds = this.totalTime - (this.minutes * 60)
      return this.padTime(seconds)
    }
  }
}
</script>
