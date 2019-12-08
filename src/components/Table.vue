<template>
  <div id="focusmeter">
  <h2>Focus Meter</h2>

  <CellItem v-for="cell in table"
             v-bind:cell=cell
             v-bind:key=cell.key
             v-on:click.native="toggle(cell)"
  >
  </CellItem>

  <p> Expecting cell: {{ expectedcell.color}} {{ expectedcell.number }}</p>
  </div>
</template>

<script>
import CellItem from './Cell.vue'

export default {
  name: 'TestTable',

  components: {
    CellItem
  },

  data () {
    return {
      table: [],
      timestamps: [],
      expectedcell: { color: 'red', number: 25 }
    }
  },

  created: function () {
    this.createTable()
  },

  methods: {
    shuffleTable: function () {
      for (var i = 0; i < 100; i++) {
        var x = Math.floor(Math.random() * 49)
        var y = Math.floor(Math.random() * 49)
        var tmp = this.table[x]
        this.table[x] = this.table[y]
        this.table[y] = tmp
      }
    },

    createTable: function () {
      for (var i = 1; i <= 24; i++) {
        this.table.push({ number: i, color: 'black', founded: false, key: i })
      }
      for (i = 1; i <= 25; i++) {
        this.table.push({ number: i, color: 'red', founded: 'false', key: 24 + i })
      }
      this.shuffleTable()
    },

    toggle: function (cell) {
      cell.founded = true
      if (this.expectedcell.number === cell.number &&
          this.expectedcell.color === cell.color) {
        this.expectedcell.color = (this.expectedcell.color === 'black') ? 'red' : 'black'
        this.expectedcell.number = (this.expectedcell.color === 'black') ? cell.number - 1 : cell.number
        console.log('OK! Now expecting:', this.expectedcell.color, this.expectedcell.number)
      }
      this.timestamps.push({ date: Date.now(), key: cell.key })
      this.lastcell = cell
      console.log(cell.number)
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
  border-radius: 4px;
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

#focusmeter {
  width: 280px;
  height: 280px;
  margin: auto;
}

#focusmeter div{
  float: left;
  width: 30px;
  height: 30px;
  padding: 5px;
  text-align: center;
  vertical-align: middle;
}

#focusmeter div:hover {
   opacity: 0.8;
}

#focusmeter .red {
  background-color: maroon;
  color: white;
}

#focusmeter .black {
  background-color: black;
  color: white;
}
</style>
