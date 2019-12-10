<template>
  <div class="home">
    <!--<img alt="Vue logo" src="../assets/logo.png" width="192px">-->
    <TestTable v-on:testing-complete="handleResults" />
  </div>
</template>

<script>
// @ is an alias to /src
import TestTable from '@/components/Table.vue'

export default {
  name: 'home',
  components: {
    TestTable
  }
  methods: {
    handleResults: function (timestamps) {
      
    }
  }
}
</script>
