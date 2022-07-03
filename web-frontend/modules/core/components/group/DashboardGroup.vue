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
          <div class="bar">
            <div
              class="progress"
              v-bind:style="{ width: '70%', 'background-color': '#16324f' }"
            ></div>
          </div>
          <div class="bar m-t-10">
            <div
              class="progress"
              v-bind:style="{ width: '60%', 'background-color': '#2a628f' }"
            ></div>
          </div>
          <div class="dashboard__group-item-esg">
            <div>-1.1kt CO2e/month</div>
            <div>S3</div>
          </div>
          <div class="bar">
            <div
              class="progress"
              v-bind:style="{ width: '50%', 'background-color': '#667761' }"
            ></div>
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
      </li>
      <li class="dashboard__group-item">
        <div class="group-item">
          <a
            ref="createApplicationContextLink"
            class="dashboard__group-item-link"
            @click="
              $refs.createApplicationContext.toggle(
                $refs.createApplicationContextLink
              )
            "
          >
            <div class="dashboard__group-item-name m-b-0">
              {{ $t('dashboardGroup.createApplication') }}
            </div></a
          >
          <CreateApplicationContext
            ref="createApplicationContext"
            :group="group"
          ></CreateApplicationContext>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

import CreateApplicationContext from '@baserow/modules/core/components/application/CreateApplicationContext'
import GroupContext from '@baserow/modules/core/components/group/GroupContext'
import editGroup from '@baserow/modules/core/mixins/editGroup'

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
