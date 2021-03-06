<!--
Copyright 2019 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->
<template>
  <tr :class="htmlClass">
    <template v-if="frozen">
      <td class="project-form-access-row-form-name">
        <span v-if="form.publishedAt == null" class="icon-edit"
          :title="$t('draftTitle')">
        </span>
        <router-link :to="primaryFormPath(form)" :title="form.nameOrId()">{{ form.nameOrId() }}</router-link>
      </td>
      <td>
        <div class="form-group">
          <select class="form-control"
            :class="{ 'uncommitted-change': stateChanged }"
            :value="changes.current.state" :aria-label="$t('field.state')"
            @change="updateState($event.target.value)">
            <option value="open">{{ $t('formState.open') }}</option>
            <option value="closing">{{ $t('formState.closing') }}</option>
            <option value="closed">{{ $t('formState.closed') }}</option>
          </select>
        </div>
      </td>
    </template>
    <template v-else>
      <td></td>
      <td v-for="fieldKey of fieldKeysWithToken" :key="fieldKey.id"
        class="project-form-access-row-access">
        <div class="checkbox">
          <label>
            <input type="checkbox"
              :class="{ 'uncommitted-change': fieldKeyAccessChanged(fieldKey) }"
              :checked="changes.current.fieldKeyAccess[fieldKey.id]"
              :aria-label="$t('field.appUserAccess')"
              @change="updateFieldKeyAccess(fieldKey, $event.target.checked)">
          </label>
        </div>
      </td>
      <td></td>
    </template>
  </tr>
</template>

<script>
import { mapGetters } from 'vuex';

import Form from '../../../presenters/form';
import routes from '../../../mixins/routes';

export default {
  name: 'ProjectFormAccessRow',
  mixins: [routes()],
  props: {
    form: {
      type: Form,
      required: true
    },
    changes: {
      type: Object,
      required: true
    },
    frozen: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    ...mapGetters(['fieldKeysWithToken']),
    htmlClass() {
      return {
        'project-form-access-row': true,
        'project-form-access-row-draft': this.form.publishedAt == null
      };
    },
    stateChanged() {
      return this.changes.current.state !== this.changes.previous.state;
    }
  },
  methods: {
    fieldKeyAccessChanged(fieldKey) {
      return this.changes.current.fieldKeyAccess[fieldKey.id] !==
        this.changes.previous.fieldKeyAccess[fieldKey.id];
    },
    updateState(state) {
      this.$emit('update:state', this.form, state);
    },
    updateFieldKeyAccess(fieldKey, accessible) {
      this.$emit('update:field-key-access', this.form, fieldKey, accessible);
    }
  }
};
</script>

<style lang="scss">
.project-form-access-row-form-name {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.project-form-access-row {
  .form-group {
    margin-bottom: 0;
    padding-bottom: 0;
  }

  .form-control {
    width: 120px;
  }

  .checkbox {
    margin-bottom: 0;
    margin-left: 6px;
    margin-top: 5px;
  }
}

.project-form-access-row-draft {
  background-color: rgba(0, 0, 0, 0.0225);

  .icon-edit {
    cursor: help;
    margin-right: 9px;
  }
}
</style>

<i18n lang="json5">
{
  "en": {
    "draftTitle": "This Form does not yet have a published version. It will not appear on devices until a Draft is published. Once you publish the Form, the settings shown here will be used.",
    "field": {
      // This is the text of a form field. "State" refers to the Form State.
      // Form States control the lifecycle state of each Form.
      "state": "State",
      "appUserAccess": "App User Access"
    }
  }
}
</i18n>
