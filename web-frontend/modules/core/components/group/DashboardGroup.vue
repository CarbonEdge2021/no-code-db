<template>
  <div class="dashboard__group">
    <div class="dashboard__group-head">
      <div
        ref="contextLink"
        class="dashboard__group-title"
        :class="{ 'dashboard__group-title-link--loading': group._.loading }"
      >
        <Editable
          ref="rename"
          :value="group.name"
          @change="renameGroup(group, $event)"
        ></Editable>
        <a
          class="dashboard__group-title-options"
          @click="$refs.context.toggle($refs.contextLink, 'bottom', 'left', 0)"
        >
          <i class="dashboard__group-title-icon fas fa-caret-down"></i>
        </a>
      </div>
      <GroupContext
        ref="context"
        :group="group"
        @rename="enableRename()"
      ></GroupContext>
      <a
        v-if="group.permissions === 'ADMIN'"
        class="dashboard__group-link"
        @click="$refs.context.showGroupMembersModal()"
        >{{ $t('dashboardGroup.showMembers') }}</a
      >
    </div>
    <ul class="dashboard__group-items">
      <li
        v-for="application in getAllOfGroup(group)"
        :key="application.id"
        class="dashboard__group-item"
      >
        <div class="group-item">
          <a
            class="dashboard__group-item-link"
            @click="selectApplication(application)"
          >
            <div class="dashboard__group-item-name">
              {{ application.name }}
            </div></a
          >

          <div class="dashboard__group-item-esg">
            <div>-2.6kt CO2e/month</div>
            <div>S1 & S2</div>
          </div>
          <!-- <b-progress
            :value="value"
            :max="max"
            class="dashboard__group-item-esg-bar"
          ></b-progress> -->
          <div class="dashboard__group-item-esg">
            <div>-1.1kt CO2e/month</div>
            <div>S3</div>
          </div>
          <div class="item-action-row">
            <label for="toggle_button" class="check-box-text">
              <span v-if="isPrivate">Private</span>
              <span v-if="!isPrivate">Public</span>
              <input
                type="checkbox"
                id="toggle_button"
                class="m-l-10"
                v-model="checkedValue"
              />
            </label>

            <i class="fas fa-cog" @click="openSettings(application)"></i>
          </div>
        </div>

        <!-- <a
          class="dashboard__group-item-link"
          @click="selectApplication(application)"
        >
          <div class="dashboard__group-item-icon">
            <i class="fas" :class="'fa-' + application._.type.iconClass"></i>
          </div>
          <div class="dashboard__group-item-name">
            {{ application.name }}
          </div>
        </a> -->
      </li>
      <li class="dashboard__group-item">
        <a
          ref="createApplicationContextLink"
          class="dashboard__group-item-link"
          @click="
            $refs.createApplicationContext.toggle(
              $refs.createApplicationContextLink
            )
          "
        >
          <div class="dashboard__group-item-name">
            {{ $t('dashboardGroup.createApplication') }}
          </div>
        </a>
        <CreateApplicationContext
          ref="createApplicationContext"
          :group="group"
        ></CreateApplicationContext>
      </li>
    </ul>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

import CreateApplicationContext from '@baserow/modules/core/components/application/CreateApplicationContext'
import GroupContext from '@baserow/modules/core/components/group/GroupContext'
import editGroup from '@baserow/modules/core/mixins/editGroup'

// import { BProgress } from 'bootstrap-vue'

// Vue.component('b-progress', BProgress)

// // Import Bootstrap and BootstrapVue CSS files (order is important)
// import 'bootstrap/dist/css/bootstrap.css'
// import 'bootstrap-vue/dist/bootstrap-vue.css'

export default {
  components: {
    CreateApplicationContext,
    GroupContext,
  },
  mixins: [editGroup],
  props: {
    group: {
      type: Object,
      required: true,
    },
    data() {
      return {
        currentState: false,
        // value: 33,
        // max: 50,
      }
    },
  },
  computed: {
    ...mapGetters({
      getAllOfGroup: 'application/getAllOfGroup',
    }),

    isPrivate() {
      return this.currentState
    },

    checkedValue: {
      get() {
        return this.defaultState
      },
      set(newValue) {
        this.currentState = newValue
      },
    },
  },
  methods: {
    selectApplication(application) {
      const type = this.$registry.get('application', application.type)
      type.select(application, this)
    },
    openSettings(application) {},
  },
}
</script>
