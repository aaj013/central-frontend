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
  <modal id="project-new" :state="state" :hideable="!awaitingResponse" backdrop
    @hide="$emit('hide')" @shown="$refs.name.focus()">
    <template #title>{{ $t('title') }}</template>
    <template #body>
      <div class="modal-introduction">
        <p>{{ $t('introduction[0]') }}</p>
        <i18n tag="p" path="moreInfo.helpArticle.full">
          <template #helpArticle>
            <doc-link to="central-projects/">{{ $t('moreInfo.helpArticle.helpArticle') }}</doc-link>
          </template>
        </i18n>
      </div>
      <form @submit.prevent="submit">
        <form-group ref="name" v-model.trim="name"
          :placeholder="$t('field.name')" required autocomplete="off"/>
        <div class="modal-actions">
          <button type="submit" class="btn btn-primary"
            :disabled="awaitingResponse">
            {{ $t('action.create') }} <spinner :state="awaitingResponse"/>
          </button>
          <button type="button" class="btn btn-link"
            :disabled="awaitingResponse" @click="$emit('hide')">
            {{ $t('action.cancel') }}
          </button>
        </div>
      </form>
    </template>
  </modal>
</template>

<script>
import DocLink from '../doc-link.vue';
import FormGroup from '../form-group.vue';
import Modal from '../modal.vue';
import Spinner from '../spinner.vue';
import request from '../../mixins/request';
import { noop } from '../../util/util';

export default {
  name: 'ProjectNew',
  components: { DocLink, FormGroup, Modal, Spinner },
  mixins: [request()],
  props: {
    state: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      awaitingResponse: false,
      name: ''
    };
  },
  watch: {
    state(state) {
      if (!state) this.name = '';
    }
  },
  methods: {
    submit() {
      this.post('/projects', { name: this.name })
        .then(({ data }) => {
          this.$emit('success', data);
        })
        .catch(noop);
    }
  }
};
</script>

<i18n lang="json5">
{
  "en": {
    // This is the title at the top of a pop-up.
    "title": "Create Project",
    "introduction": [
      "Projects group Forms and App Users together to make them easier to organize and manage, both on this website and on your data collection device."
    ]
  }
}
</i18n>
