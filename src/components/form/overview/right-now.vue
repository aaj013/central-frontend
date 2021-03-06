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
  <page-section id="form-overview-right-now"
    :class="{ 'open-form': form.state === 'open' }" condensed>
    <template #heading>
      <span>{{ $t('common.rightNow') }}</span>
    </template>
    <template #body>
      <form-version-summary-item :version="form">
        <template #body>
          <i18n tag="p" path="version.full">
            <template #publishedVersion>
              <strong>{{ $t('version.publishedVersion') }}</strong>
            </template>
          </i18n>
        </template>
      </form-version-summary-item>
      <summary-item :icon="stateIcon">
        <template #heading>
          {{ $t(`formState.${form.state}`) }}
        </template>
        <template #body>
          {{ $t(`stateCaption.${form.state}`) }}
        </template>
      </summary-item>
      <summary-item :route-to="formPath('submissions')" icon="inbox">
        <template #heading>
          {{ $n(form.submissions, 'default') }}
          <span class="icon-angle-right"></span>
        </template>
        <template #body>
          <i18n tag="p" :path="$tcPath('submissions.full', form.submissions)">
            <template #submissions>
              <strong>{{ $tc('submissions.submissions', form.submissions) }}</strong>
            </template>
          </i18n>
        </template>
      </summary-item>
    </template>
  </page-section>
</template>

<script>
import FormVersionSummaryItem from '../../form-version/summary-item.vue';
import PageSection from '../../page/section.vue';
import SummaryItem from '../../summary-item.vue';
import routes from '../../../mixins/routes';
import { requestData } from '../../../store/modules/request';

export default {
  name: 'FormOverviewRightNow',
  components: { FormVersionSummaryItem, PageSection, SummaryItem },
  mixins: [routes()],
  computed: {
    // The component assumes that this data will exist when the component is
    // created.
    ...requestData(['form']),
    stateIcon() {
      switch (this.form.state) {
        case 'open':
          return 'exchange';
        case 'closing':
          return 'clock-o';
        default: // 'closed'
          return 'lock';
      }
    }
  }
};
</script>

<style lang="scss">
#form-overview-right-now {
  // .icon-lock is a little more narrow than .icon-file-o and .icon-inbox, so we
  // use this to center it.
  .icon-lock {
    margin-left: 6px;
    margin-right: 6px;
  }

  &.open-form {
    .icon-file-o, .icon-inbox {
      margin-left: 4px;
      margin-right: 4px;
    }
  }
}
</style>

<i18n lang="json5">
{
  "en": {
    "version": {
      "full": "{publishedVersion} of this Form.",
      "publishedVersion": "Published version"
    },
    "stateCaption": {
      "open": "This Form is downloadable and is accepting Submissions.",
      "closing": "This Form is not downloadable but still accepts Submissions.",
      "closed": "This Form is not downloadable and does not accept Submissions."
    },
    "submissions": {
      // The count of Submissions is shown separately above this text.
      "full": [
        "{submissions} has been saved for this Form.",
        "{submissions} have been saved for this Form."
      ],
      "submissions": "Submission | Submissions"
    }
  }
}
</i18n>
