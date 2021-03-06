<template>
  <div id="my-account">
    <SfBreadcrumbs class="breadcrumbs desktop-only" :breadcrumbs="breadcrumbs">
      <template #link="{breadcrumb}">
        <router-link :to="breadcrumb.route.link" class="sf-breadcrumbs__link">
          {{ breadcrumb.text }}
        </router-link>
      </template>
    </SfBreadcrumbs>
    <SfContentPages
      :title="$t('My Account')"
      :active="activePage"
      class="my-account"
      @click:change="changeActivePage"
    >
      <SfContentCategory :title="$t('Personal Details')">
        <SfContentPage :title="$t('My profile')">
          <OMyAccountProfile />
        </SfContentPage>
        <SfContentPage :title="$t('Shipping details')">
          <OMyAccountShippingDetails />
        </SfContentPage>
        <SfContentPage :title="$t('Loyalty Card')">
          <OMyAccountPlaceholder :title="$t('Loyalty Card')" />
        </SfContentPage>
        <SfContentPage :title="$t('My newsletter')">
          <OMyAccountPlaceholder :title="$t('My newsletter')" />
        </SfContentPage>
      </SfContentCategory>
      <SfContentCategory :title="$t('Order details')">
        <SfContentPage :title="$t('Order history')">
          <OMyAccountOrdersHistory />
        </SfContentPage>
        <SfContentPage :title="$t('My reviews')">
          <OMyAccountPlaceholder :title="$t('My reviews')" />
        </SfContentPage>
      </SfContentCategory>
      <SfContentPage :title="$t('Log out')" />
    </SfContentPages>
  </div>
</template>

<script>
import MyAccount from '@vue-storefront/core/pages/MyAccount';

import OMyAccountProfile from 'theme/components/organisms/o-my-account-profile';
import OMyAccountShippingDetails from 'theme/components/organisms/o-my-account-shipping-details'
import OMyAccountOrdersHistory from 'theme/components/organisms/o-my-account-orders-history';
import OMyAccountPlaceholder from 'theme/components/organisms/o-my-account-placeholder';
import { localizedRoute } from '@vue-storefront/core/lib/multistore';
import { SfBreadcrumbs, SfContentPages } from '@storefront-ui/vue';

export default {
  components: {
    SfBreadcrumbs,
    SfContentPages,
    OMyAccountProfile,
    OMyAccountShippingDetails,
    OMyAccountOrdersHistory,
    OMyAccountPlaceholder
  },
  mixins: [MyAccount],
  data () {
    return {
      activePage: this.$t('My profile'),
      breadcrumbs: [
        {
          'text': this.$t('Home'),
          'route': {
            'link': localizedRoute('/')
          }
        },
        {
          'text': this.$t('My account'),
          'route': {
            'link': localizedRoute('/my-account')
          }
        }
      ]
    };
  },
  methods: {
    changeActivePage (title) {
      if (title === 'Log out') {
        this.logout();
        return;
      }
      this.activePage = title;
    },
    async logout () {
      await this.$store.dispatch('user/logout', {});
      this.$router.push(this.localizedRoute('/'));
    }
  }
};
</script>

<style lang="scss" scoped>
@import "~@storefront-ui/vue/styles";

#my-account {
  box-sizing: border-box;
  @include for-desktop {
    max-width: 1240px;
    margin: auto;
  }
}

.breadcrumbs {
  padding: var(--spacer-big) var(--spacer-extra-big) var(--spacer-extra-big);
}

.main {
  display: flex;
}
</style>
