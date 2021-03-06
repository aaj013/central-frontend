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
  <div id="account-login" class="row">
    <div class="col-xs-12 col-sm-offset-3 col-sm-6">
      <div class="panel panel-default panel-main">
        <div class="panel-heading">
          <h1 class="panel-title">{{ $t('title') }}</h1>
        </div>
        <div class="panel-body">
          <form @submit.prevent="submit">
            <form-group ref="email" v-model.trim="email" type="email"
              :placeholder="$t('field.email')" required autocomplete="off"/>
            <form-group v-model="password" type="password"
              :placeholder="$t('field.password')" required
              autocomplete="current-password"/>
            <div class="panel-footer">
              <button type="submit" class="btn btn-primary"
                :disabled="disabled">
                {{ $t('action.logIn') }} <spinner :state="disabled"/>
              </button>
              <router-link to="/reset-password" tag="button" type="button"
                class="btn btn-link" :disabled="disabled">
                {{ $t('action.resetPassword') }}
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

export default {
  name: 'AccountLogin',
  components: { FormGroup, Spinner },
  mixins: [request()],
  data() {
    return {
      disabled: false,
      email: '',
      password: ''
    };
  },
  mounted() {
    this.$refs.email.focus();
  },
  beforeRouteLeave(to, from, next) {
    if (this.disabled)
      next(false);
    else
      next();
  },
  methods: {
    navigateToNext(
      next,
      // Function that redirects within Frontend
      internal,
      // Function that redirects outside Frontend
      external
    ) {
      if (typeof next !== 'string') return internal('/');
      let url;
      try {
        url = new URL(next, window.location.origin);
      } catch (e) {
        return internal('/');
      }
      if (url.origin !== window.location.origin || url.pathname === '/login')
        return internal('/');

      if (url.pathname.startsWith('/enketo/')) return external(url.href);
      return internal(url.pathname + url.search + url.hash);
    },
    submit() {
      this.disabled = true;
      this.request({
        method: 'POST',
        url: '/sessions',
        data: { email: this.email, password: this.password }
      })
        .then(({ data }) => this.$store.dispatch('get', [{
          key: 'currentUser',
          url: '/users/current',
          headers: { Authorization: `Bearer ${data.token}` },
          extended: true,
          success: () => {
            this.$store.commit('setData', { key: 'session', value: data });
          }
        }]))
        .then(() => {
          this.navigateToNext(
            this.$route.query.next,
            (location) => {
              // We only set this.disabled to `false` before redirecting within
              // Frontend. If we also set this.disabled before redirecting
              // outside Frontend, there might be a moment before the user is
              // redirected when buttons are re-enabled.
              this.disabled = false;
              this.$router.replace(location);
            },
            (url) => {
              window.location.replace(url);
            }
          );
        })
        .catch(() => {
          this.disabled = false;
        });
    }
  }
};
</script>

<i18n lang="json5">
{
  "en": {
    // This is a title shown above a section of the page.
    "title": "Log in",
    "problem": {
      "401_2": "Incorrect email address and/or password."
    }
  }
}
</i18n>
