<template>
  <div id="content-view">
    <div class="row">
      <div class="col-sm-auto info-bar">
          <h3 class="mg-header" v-if="isSignedIn">{{$t('lifelines-webshop-content-header')}}</h3>
          <h3 class="mg-header" v-else>{{$t('lifelines-webshop-signed-out-content-header')}}</h3>
      </div>
    </div>

    <div class="row flex-nowrap mb-5" >
      <tree-view  class="col-sm-auto info-bar" />

      <div class="col" >
        <search-component
          :searchTerm="searchTerm"
          :searching="isGridLoading"
          @searchChanged="onSearchChange"
          class="mb-2"
        ></search-component>
        <grid-view />
      </div>
    </div>

  </div>
</template>

<script>
import Vue from 'vue'
import TreeView from './TreeView.vue'
import GridView from './GridView.vue'
import { mapMutations, mapActions, mapGetters, mapState } from 'vuex'
import SearchComponent from '../components/search/SearchComponent.vue'

export default Vue.extend({
  name: 'ContentView',
  components: { TreeView, GridView, SearchComponent },
  computed: {
    ...mapGetters(['isSignedIn', 'isGridLoading']),
    ...mapState(['searchTerm'])
  },
  methods: {
    ...mapMutations(['updateSearchTerm']),
    onSearchChange (value) {
      this.updateSearchTerm(value || null)
    }
  }
})
</script>

<style scoped>
  .col.tree {
    max-width: 22rem;
  }
</style>
