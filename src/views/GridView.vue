<template>
  <div id="Grid-view">
    <Grid />
  </div>
</template>

<script>
import Vue from 'vue'
import Grid from '../components/Grid/GridComponent.vue'

export default Vue.extend({
  name: 'ContentView',
  components: { Grid }
})
</script>