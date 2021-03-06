<!--
Copyright 2020 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->
<template>
  <ul id="navbar-links" class="nav navbar-nav">
    <li :class="{ active: projectsLinkIsActive }">
      <router-link id="navbar-links-projects" to="/">
        {{ $t('projects') }}
        <span v-show="projectsLinkIsActive" class="sr-only">
          {{ $t('current') }}
        </span>
      </router-link>
    </li>
    <li v-if="canRoute('/users')"
      :class="{ active: routePathStartsWith('/users') }">
      <router-link id="navbar-links-users" to="/users">
        {{ $t('users') }}
        <span v-show="routePathStartsWith('/users')" class="sr-only">
          {{ $t('current') }}
        </span>
      </router-link>
    </li>
    <li v-if="canRoute('/system/backups')"
      :class="{ active: routePathStartsWith('/system') }">
      <router-link id="navbar-links-system" to="/system/backups">
        {{ $t('system') }}
        <span v-show="routePathStartsWith('/system')" class="sr-only">
          {{ $t('current') }}
        </span>
      </router-link>
    </li>
  </ul>
</template>

<script>
import routes from '../../mixins/routes';

export default {
  name: 'NavbarLinks',
  mixins: [routes()],
  computed: {
    projectsLinkIsActive() {
      return this.$route.path === '/' || this.routePathStartsWith('/projects');
    }
  },
  methods: {
    routePathStartsWith(path) {
      return this.$route.path === path ||
        this.$route.path.startsWith(`${path}/`);
    }
  }
};
</script>

<style lang="scss">
@media (min-width: 768px) {
  #navbar-links-projects, #navbar-links-users {
    margin-left: 30px;
  }
}
</style>

<i18n lang="json5">
{
  "en": {
    // This is the text of a link shown in the navigation bar at the top of the
    // page.
    "projects": "Projects",
    // This is the text of a link shown in the navigation bar at the top of the
    // page.
    "users": "Users",
    // This is the text of a link shown in the navigation bar at the top of the
    // page.
    "system": "System",
    // Used by screen readers to identify the currently-selected navigation tab
    "current": "current"
  }
}
</i18n>
