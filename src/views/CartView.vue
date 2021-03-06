<template>
  <div id="cart-view">
    {{ $t('lifelines-webshop-cart-empty-variables-warning') }}
    <div class="container-fluid">
      <div class="row">
        <div class="col-sm-12 col-md-12 col-lg-6">
          <h3 class="h4">{{$t('lifelines-webshop-cart-header')}}</h3>

          <spinner-animation v-if="loading" />

          <div v-if="selectedVariableIds.length === 0">
            <h4 class="h5">No variables selected</h4>
            <p v-if="isSignedIn">{{$t('lifelines-webshop-cart-info-msg')}}</p>
            <p v-else>{{$t('lifelines-webshop-cart-not-signedin-msg')}}</p>
          </div>

          <div v-else class="mt-3 mb-2" role="tablist">
            <div class="mb-2 d-flex justify-content-end">
              <button
                type="button"
                class="btn btn-primary save"
                @click="onSave"
              >{{$t('lifelines-webshop-save-btn-label')}}</button>
              <router-link
                class="btn btn-success ml-2"
                type="button"
                to="/order"
                tag="button"
              >{{$t('lifelines-webshop-order-btn-label')}}</router-link>
            </div>
            <b-card no-body class="mb-2" v-for="(section, index) in cartTree" :key="section.id">
              <b-card-header
                header-tag="header"
                class="pl-1 pt-2 pr-2 pb-2 hoverable"
                role="tab"
                @click.stop="collapseStatus(index)"
              >
                <h5 class="pl-3 m-0 text-black">
                  {{section.name}}
                  <collapse-tree-icon class="ml-2" :state="isActive(index)"></collapse-tree-icon>
                </h5>
              </b-card-header>

              <!-- Don't use the same accordion index, so all items can be expanded at the same time -->
              <b-collapse
                :visible="isActive(index)"
                :id="`accordion-${index}`"
                :accordion="`my-accordion-${index}`"
                role="tabpanel"
              >
                <b-card-body>
                  <div v-for="subsection in section.subsections" :key="subsection.id">
                    <h5 class="h6">{{subsection.name}}</h5>
                    <ul>
                      <li v-for="variable in subsection.variables" :key="variable.id">
                        <span>{{variable.label||variable.name}} {{ variableAssesments[variable.id] }}</span>
                      </li>
                    </ul>
                  </div>
                </b-card-body>
              </b-collapse>
            </b-card>
          </div>

          <div class="mb-3" v-if="selectedVariableIds.length > 10">
            <div class="d-flex justify-content-end">
              <button
                type="button"
                class="btn btn-primary save"
                @click="onSave"
              >{{$t('lifelines-webshop-save-btn-label')}}</button>
              <router-link
                class="btn btn-success ml-2"
                type="button"
                to="/order"
                tag="button"
              >{{$t('lifelines-webshop-order-btn-label')}}</router-link>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import SpinnerAnimation from '../components/animations/SpinnerAnimation'
import { mapActions, mapGetters, mapState, mapMutations } from 'vuex'
import CollapseTreeIcon from '@/components/animations/CollapseTreeIcon'

export default Vue.extend({
  name: 'CartView',
  components: { SpinnerAnimation, CollapseTreeIcon },
  data () {
    return {
      openItems: ['accordion-0']
    }
  },
  created () {
    this.setToast({ type: 'info', textType: 'light', message: 'Please sign in to select and order variables' })
  },
  destroyed () {
    this.removeToast({ type: 'info', textType: 'light', message: 'Please sign in to select and order variables' })
  },
  methods: {
    ...mapMutations(['setToast', 'removeToast']),
    ...mapActions(['save']),
    async onSave () {
      const orderNumber = await this.save()
      this.$router.push({ name: 'load', params: { orderNumber } })
    },
    collapseStatus (index) {
      let clickedItem = `accordion-${index}`
      let indexOfClickedItem = this.openItems.indexOf(clickedItem)

      if (indexOfClickedItem >= 0) {
        this.openItems.splice(indexOfClickedItem, 1)
      } else {
        this.openItems.push(clickedItem)
      }
    },
    isActive (index) {
      let clickedItem = `accordion-${index}`
      return this.openItems.indexOf(clickedItem) >= 0
    }
  },
  computed: {
    ...mapGetters(['cartTree', 'isSignedIn']),
    ...mapState(['gridSelection', 'variables', 'assessments']),
    selectedVariableIds () {
      return Object.keys(this.gridSelection)
    },
    variableAssesments () {
      let variableAssesmentsStings = {}
      for (const [variableId, assessmentIds] of Object.entries(
        this.gridSelection
      )) {
        const assessmentNames = assessmentIds.map(
          assessmentId => this.assessments[assessmentId].name
        )
        variableAssesmentsStings[variableId] =
          '( ' + assessmentNames.join(', ') + ' )'
      }

      return variableAssesmentsStings
    },
    loading () {
      return !(
        Object.keys(this.assessments).length &&
        Object.keys(this.variables).length
      )
    }
  }
})
</script>

<style lang="scss" scoped>
/deep/ .card {
  ul {
    font-size: 0.9rem;
    list-style: none;
    padding: 0;
  }
}

.hoverable {
  cursor: pointer;
}
</style>
