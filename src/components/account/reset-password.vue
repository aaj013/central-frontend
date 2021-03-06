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
  <div id="account-reset-password" class="row">
    <div class="col-xs-12 col-sm-offset-3 col-sm-6">
      <div class="panel panel-default panel-main">
        <div class="panel-heading">
          <h1 class="panel-title">{{ $t('title') }}</h1>
        </div>
        <div class="panel-body">
          <form @submit.prevent="submit">
            <form-group ref="email" v-model.trim="email" type="email"
              :placeholder="$t('field.email')" required autocomplete="off"/>
            <div class="panel-footer">
              <button type="submit" class="btn btn-primary"
                :disabled="awaitingResponse">
                {{ $t('action.resetPassword') }} <spinner :state="awaitingResponse"/>
              </button>
              <router-link to="/login" tag="button" type="button"
                class="btn btn-link">
                {{ $t('action.cancel') }}
              </router-link>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import FormGroup from '../form-group.vue';
import Spinner from '../spinner.vue';
import request from '../../mixins/request';
import { noop } from '../../util/util';

export default {
  name: 'AccountResetPassword',
  components: { FormGroup, Spinner },
  mixins: [request()],
  data() {
    return {
      awaitingResponse: false,
      email: ''
    };
  },
  mounted() {
    this.$refs.email.focus();
  },
  methods: {
    submit() {
      this
        .post('/users/reset/initiate', { email: this.email })
        .then(() => this.$router.push('/login', () => {
          this.$alert().success(this.$t('alert.success', {
            email: this.email
          }));
        }))
        .catch(noop);
    }
  }
};
</script>

<i18n lang="json5">
{
  "en": {
    // This is a title shown above a section of the page.
    "title": "Reset Password",
    "alert": {
      "success": "An email has been sent to {email} with further instructions."
    }
  }
}
</i18n>
