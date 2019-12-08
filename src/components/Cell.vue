<template>
<div :class="cell.color">
<span>{{cell.number}}</span>
</div>
</template>

<script>
export default {
  name: 'CellItem',
  props: ['cell']
}
</script>
