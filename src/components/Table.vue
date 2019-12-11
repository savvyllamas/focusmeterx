<template>
  <div id="focusmeter">
    <Stopwatch
      v-bind:state=state
      v-on:testing-start="startTesting"
      v-on:testing-stop="stopTesting"
      v-on:testing-reset="resetTesting"
      />
    <section id="gorbov-table">
      <CellItem
       v-for="cell in table"
       v-bind:cell=cell
       v-bind:key=cell.key
       v-on:click.native="toggle(cell)"
      >
    </CellItem>
    </section>
    <h3>{{mode}}/2</h3>
    <p>Expecting cell: {{ expectedcell.color }} {{ expectedcell.number }}</p>
    <div>{{ title }}</div>
  </div>
</template>

<script>
import CellItem from './Cell.vue'
import Stopwatch from './Stopwatch.vue'

export default {
  name: 'TestTable',

  components: {
    CellItem,
    Stopwatch
  },

  data () {
    return {
      state: 'stopped',
      title: '',
      table: [],
      mode: 1,
      timestamps: [],
      expectedcell: {},
      expected: [],
      testing: {}
    }
  },
  created: function () {
    // this.createTable()
  },

  methods: {
    startTesting: function (startTime) {
      this.state = 'going'
      console.log('Testing started')
      this.timestamps.push({ event: 'start', time: startTime })
      this.table.length = 0
      this.expected.length = 0
      this.calc_expected_seq()
      this.expectedcell = this.expected.shift()
      this.createTable()
    },
    stopTesting: function (stopTime) {
      // this.state = 'stopped'
    },
    resetTesting: function (resetTime) {
      // this.state = 'reseted'
      console.log('Testing reseted')
      this.resetTable()
    },
    completeTesting: function (completeTime) {
      this.state = 'stopped'
      this.title = 'Тест пройден'
      this.timestamps.push({ event: 'finish', time: completeTime })
      if (this.mode === 1) {
        this.mode = 2
        this.startTesting()
      } else {
        this.$emit('testing-complete', this.timestamps, this.mode)
      }
      this.testing = {
        'mode': this.mode,
        'startedAt': this.timestamps[0],
        'finishAt': this.timestamps[this.timestamps.length - 1],
        'clicks': this.timestamps.length,
        'times': {
          'red': { min: 999, avg: 0, max: -999, total: 0 },
          'black': { min: 999, avg: 0, max: -999, total: 0 }
        }
      }
      let prevStamp = this.timestamps[0]
      for (let stamp of this.timestamps) {
        if (stamp.event === 'found') {
          stamp['span'] = Math.abs(stamp.time - prevStamp.time) / 1000
          let c = stamp['cell'].color
          this.testing.times[c].max = Math.max(this.testing.times[c].max, stamp.span)
          this.testing.times[c].min = Math.min(this.testing.times[c].min, stamp.span)
          this.testing.times[c].total += stamp.span
          prevStamp = stamp
        }
      }
      this.testing.times.black.avg = this.testing.times.black.total / 24
      this.testing.times.red.avg = this.testing.times.red.total / 25
    },
    shuffleTable: function () {
      for (var i = 0; i < 100; i++) {
        var x = Math.floor(Math.random() * 49)
        var y = Math.floor(Math.random() * 49)
        var tmp = this.table[x]
        this.table[x] = this.table[y]
        this.table[y] = tmp
      }
      this.$forceUpdate()
    },

    createTable: function () {
      for (let i = 1; i <= 24; i++) {
        this.table.push({ number: i, color: 'black', found: false, key: i })
      }
      for (let i = 1; i <= 25; i++) {
        this.table.push({ number: i, color: 'red', found: false, key: 24 + i })
      }
      this.shuffleTable()
    },

    resetTable: function () {
      this.shuffleTable()
      for (let i = 1; i <= 49; i++) {
        this.table[i].found = false
      }
      this.expectedcell = { color: 'red', number: 25 }
    },
    is_expected: function (cell) {
      return (this.expectedcell.number === cell.number &&
            this.expectedcell.color === cell.color)
    },
    calc_expected_seq: function (cell) {
    // mode1: 1b ->  2b -> .. -> 24b -> 25r -> 24r -> .. -> 1r
    // mode2: 1b -> 25r -> 2b -> 24r -> ..  -> 24b -> 1r
      if (this.mode === 1) {
        for (let i = 1; i <= 24; i++) {
          this.expected.push({ color: 'black', number: i })
        }
        for (let i = 25; i >= 1; i--) {
          this.expected.push({ color: 'red', number: i })
        }
      }
      if (this.mode === 2) {
        for (let i = 1, j = 24; i <= 25 || j >= 0; i++, j--) {
          this.expected.push({ color: 'red', number: i })
          this.expected.push({ color: 'black', number: j })
        }
        this.expected.pop()
      }
    },
    toggle: function (cell) {
      if (this.is_expected(cell)) {
        cell.found = true
        if (this.expected.length > 0) {
          this.expectedcell = this.expected.shift()
        } else {
          this.completeTesting(Date.now())
        }
      }

      this.timestamps.push({
        'event': (cell.found ? 'found' : 'missed'),
        'time': Date.now(),
        'cell': {
          'color': cell.color,
          'number': cell.number
        },
        'key': cell.key
      })
    }
  }
}

</script>

<style scope>
body {
  background: #20262E;
  padding: 20px;
  font-family: Helvetica;
}

#app {
  background: #fff;
  border-radius: 10px;
  padding: 20px;
  text-align: center;
  transition: all 0.2s;
}

li {
  margin: 8px 0;
}

h2 {
  font-weight: bold;
  margin-bottom: 15px;
}

del {
  color: rgba(0, 0, 0, 0.3);
}

#gorbov-table {
  width: 280px;
  height: 280px;
  margin: auto;
  clear: both
}

#gorbov-table div{
  float: left;
  width: 30px;
  height: 30px;
  padding: 5px;
  text-align: center;
  vertical-align: middle;
}

#gorbov-table div:hover {
   opacity: 0.8;
}

#gorbov-table .red {
  background-color: maroon;
  color: white;
}

#gorbov-table .black {
  background-color: black;
  color: white;
}
</style>
