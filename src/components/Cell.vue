<template>
<div>
<div
  :class="cell.color"
  v-if="cell.found"
  style="opacity: 0.8">
    <span>{{cell.number}}</span>
</div>
<div
  :class="cell.color"
  v-if="!(cell.found)">
    <span>{{cell.number}}</span>
</div>
</div>
</template>

<script>
export default {
  name: 'CellItem',
  props: ['cell']
}
</script>
