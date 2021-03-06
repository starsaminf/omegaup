<template>
  <div class="card panel panel-primary">
    <div class="card-body panel-body">
      <form class="form" @submit.prevent="onSubmit">
        <div class="form-group">
          <label class="font-weight-bold"
            >{{ T.wordsGroupAdmin }}
            <font-awesome-icon
              :title="T.courseEditAddGroupAdminsTooltip"
              icon="info-circle"
            />
            <omegaup-autocomplete
              class="form-control"
              :init="(el) => typeahead.groupTypeahead(el)"
              :value.sync="groupAlias"
            ></omegaup-autocomplete>
          </label>
        </div>
        <button class="btn btn-primary" type="submit">
          {{ T.contestAddgroupAddGroup }}
        </button>
      </form>
    </div>
    <div v-if="groupAdmins.length === 0">
      <div class="empty-table-message">
        {{ T.courseEditGroupAdminsEmpty }}
      </div>
    </div>
    <table v-else class="table table-striped">
      <thead>
        <tr>
          <th>{{ T.contestEditRegisteredGroupAdminName }}</th>
          <th>{{ T.contestEditRegisteredAdminRole }}</th>
          <th>{{ T.contestEditRegisteredAdminDelete }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="groupAdmin in groupAdmins">
          <td>
            <a :href="`/group/${groupAdmin.alias}/edit/`">
              {{ groupAdmin.name }}
            </a>
          </td>
          <td>{{ groupAdmin.role }}</td>
          <td>
            <button
              v-if="groupAdmin.name !== 'admin'"
              class="close"
              type="button"
              @click="onRemove(groupAdmin)"
            >
              &times;
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop, Watch } from 'vue-property-decorator';
import { omegaup } from '../../omegaup';
import T from '../../lang';
import * as typeahead from '../../typeahead';
import Autocomplete from '../Autocomplete.vue';

import {
  FontAwesomeIcon,
  FontAwesomeLayers,
  FontAwesomeLayersText,
} from '@fortawesome/vue-fontawesome';
import { fas } from '@fortawesome/free-solid-svg-icons';
import { library } from '@fortawesome/fontawesome-svg-core';
library.add(fas);

@Component({
  components: {
    'omegaup-autocomplete': Autocomplete,
    'font-awesome-icon': FontAwesomeIcon,
    'font-awesome-layers': FontAwesomeLayers,
    'font-awesome-layers-text': FontAwesomeLayersText,
  },
})
export default class GroupAdmin extends Vue {
  @Prop() initialGroups!: omegaup.ContestGroupAdmin[];
  @Prop({ default: false }) hasParentComponent!: boolean;

  T = T;
  typeahead = typeahead;
  groupAlias = '';
  selected: omegaup.ContestGroupAdmin = {};
  groupAdmins = this.initialGroups;

  @Watch('initialGroups')
  onAdminsChanged(newValue: omegaup.ContestGroupAdmin[]): void {
    this.groupAdmins = newValue;
  }

  onSubmit(): void {
    if (this.hasParentComponent) {
      this.$emit('emit-add-group-admin', this);
      return;
    }
    this.$emit('add-group-admin', this.groupAlias);
  }

  onRemove(group: omegaup.ContestGroupAdmin): void {
    if (this.hasParentComponent) {
      this.selected = group;
      this.$emit('emit-remove-group-admin', this);
      return;
    }
    this.$emit('remove-group-admin', group.alias);
  }
}
</script>

<style lang="scss">
@import '../../../../sass/main.scss';
</style>
