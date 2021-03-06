<!--
Copyright 2017 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->
<template>
  <tr :class="{ success: fieldKey.id === highlighted }">
    <td>{{ fieldKey.displayName }}</td>
    <td>
      <time-and-user :iso="fieldKey.createdAt" :user="fieldKey.createdBy"/>
    </td>
    <td><date-time :iso="fieldKey.lastUsed"/></td>
    <td>
      <a v-if="fieldKey.token != null" ref="popoverLink" href="#"
        class="field-key-row-popover-link" role="button"
        @click.prevent="showCode">
        <span class="icon-qrcode"></span>{{ $t('seeCode') }}
      </a>
      <template v-else>
        {{ $t('accessRevoked') }}
      </template>
    </td>
    <td>
      <div class="dropdown">
        <button :id="actionsId" type="button"
          class="btn btn-primary dropdown-toggle" data-toggle="dropdown"
          aria-haspopup="true" aria-expanded="false">
          <span class="icon-cog"></span><span class="caret"></span>
        </button>
        <ul :aria-labelledby="actionsId" class="dropdown-menu">
          <li :class="{ disabled: fieldKey.token == null }">
            <a href="#" @click.prevent="revoke">{{ $t('action.revokeAccess') }}&hellip;</a>
          </li>
        </ul>
      </div>
    </td>
  </tr>
</template>

<script>
import DateTime from '../date-time.vue';
import TimeAndUser from '../time-and-user.vue';

export default {
  name: 'FieldKeyRow',
  components: { DateTime, TimeAndUser },
  props: {
    fieldKey: {
      type: Object,
      required: true
    },
    highlighted: Number // eslint-disable-line vue/require-default-prop
  },
  computed: {
    actionsId() {
      return `field-key-row-actions${this.fieldKey.id}`;
    }
  },
  methods: {
    showCode() {
      this.$emit('show-code', this.fieldKey, this.$refs.popoverLink);
    },
    revoke() {
      // Bootstrap does not truly disable dropdown menu items marked as
      // disabled.
      if (this.fieldKey.token == null) return;
      this.$emit('revoke', this.fieldKey);
    }
  }
};
</script>

<i18n lang="json5">
{
  "en": {
    "seeCode": "See code",
    "accessRevoked": "Access revoked",
    "action": {
      "revokeAccess": "Revoke access"
    }
  }
}
</i18n>
