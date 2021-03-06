<template>
  <div class="default-layout">
    <loader />
    <div id="viewport" class="w-100 relative">
      <OHeader />
      <SfSidebar
        :visible="isSearchPanelOpen"
        class="sf-sidebar--right sidebar__search"
        @close="$store.commit('ui/setSearchpanel')"
      >
        <component
          v-if="isSearchPanelOpen"
          :is="searchPanelAsyncComponent"
          @close="$store.commit('ui/setSearchpanel')"
          @reload="reloadComponent('searchPanelAsyncComponent')"
        />
      </SfSidebar>
      <SfSidebar
        :visible="isMicrocartOpen"
        class="sf-sidebar--right"
        @close="$store.commit('ui/setMicrocart')"
      >
        <component
          v-if="isMicrocartOpen"
          :is="microcartAsyncComponent"
          @close="$store.commit('ui/setMicrocart')"
          @reload="reloadComponent('microcartAsyncComponent')"
        />
      </SfSidebar>
      <slot />
      <OFooter />
      <OModal />
      <ONotification />
      <CookieNotification />
      <OfflineBadge />
      <OrderConfirmation
        v-if="loadOrderConfirmation"
        :orders-data="ordersData"
      />
      <OBottomNavigation />
    </div>
    <vue-progress-bar />
  </div>
</template>

<script>
import { mapState } from 'vuex';
import OHeader from 'theme/components/organisms/o-header';
import OFooter from 'theme/components/organisms/o-footer';
import OModal from 'theme/components/organisms/o-modal';
import OBottomNavigation from 'theme/components/organisms/o-bottom-navigation';
import Loader from 'theme/components/core/Loader';
import ONotification from 'theme/components/organisms/o-notification';
import CookieNotification from 'theme/components/core/CookieNotification';
import OfflineBadge from 'theme/components/core/OfflineBadge';
import { isServer } from '@vue-storefront/core/helpers';
import Head from 'theme/head';
import config from 'config';
import { SfSidebar } from '@storefront-ui/vue';
import LoadingSpinner from 'theme/components/theme/blocks/AsyncSidebar/LoadingSpinner';
import LoadingError from 'theme/components/theme/blocks/AsyncSidebar/LoadingError';

const SearchPanel = () =>
  import(/* webpackChunkName: "vsf-search-panel" */ 'theme/components/core/blocks/SearchPanel/SearchPanel');
const OMicrocart = () =>
  import(/* webpackChunkName: "vsf-microcart" */ 'theme/components/organisms/o-microcart');
const OrderConfirmation = () =>
  import(/* webpackChunkName: "vsf-modals" */ 'theme/components/core/blocks/Checkout/OrderConfirmation');

export default {
  components: {
    OHeader,
    OFooter,
    Loader,
    ONotification,
    CookieNotification,
    OfflineBadge,
    OrderConfirmation,
    OBottomNavigation,
    SfSidebar,
    OModal
  },
  data () {
    return {
      loadOrderConfirmation: false,
      ordersData: [],
      microcartAsyncComponent: () => ({
        component: OMicrocart(),
        loading: LoadingSpinner,
        error: LoadingError,
        timeout: 3000
      }),
      searchPanelAsyncComponent: () => ({
        component: SearchPanel(),
        loading: LoadingSpinner,
        error: LoadingError,
        timeout: 3000
      })
    };
  },
  computed: {
    ...mapState({
      isSearchPanelOpen: state => state.ui.searchpanel,
      isMicrocartOpen: state => state.ui.microcart
    })
  },
  beforeMount () {
    // Progress bar on top of the page
    this.$router.beforeEach((to, from, next) => {
      this.$Progress.start();
      this.$Progress.increase(40);
      next();
    });
    this.$router.afterEach(() => {
      this.$Progress.finish();
    });
    this.$bus.$on('offline-order-confirmation', this.onOrderConfirmation);
  },
  mounted () {
    this.$store.dispatch('ui/checkWebpSupport');
  },
  beforeDestroy () {
    this.$bus.$off('offline-order-confirmation', this.onOrderConfirmation);
  },
  methods: {
    onOrderConfirmation (payload) {
      this.loadOrderConfirmation = true;
      this.ordersData = payload;
      this.$bus.$emit('modal-show', 'modal-order-confirmation');
    },
    fetchMenuData () {
      return this.$store.dispatch('category/list', {
        level:
          config.entities.category.categoriesDynamicPrefetch &&
          config.entities.category.categoriesDynamicPrefetchLevel >= 0
            ? config.entities.category.categoriesDynamicPrefetchLevel
            : null,
        includeFields:
          config.entities.optimize && isServer
            ? config.entities.category.includeFields
            : null,
        skipCache: isServer
      });
    },
    reloadComponent (componentName) {
      const components = {
        microcartAsyncComponent: OMicrocart,
        searchPanelAsyncComponent: SearchPanel
      };
      this[componentName] = () => ({
        component: components[componentName](),
        loading: LoadingSpinner,
        error: LoadingError,
        timeout: 3000
      });
    }
  },
  serverPrefetch () {
    return this.fetchMenuData();
  },
  metaInfo: Head
};
</script>

<style lang="scss" src="theme/css/main.scss"></style>
<style lang="scss">
@import "~@storefront-ui/shared/styles/helpers";
body {
  --overlay-z-index: 1;
  --sidebar-aside-z-index: 2;
  color: var(--c-text);
  font-size: var(--font-size-regular);
  font-family: var(--body-font-family-secondary);
  font-weight: var(--body-font-weight-primary);
  margin: 0;
  padding: 0;
  a {
    text-decoration: none;
    color: var(--c-link);
    cursor: pointer;
    &:hover {
      color: var(--c-link-hover);
    }
  }
}
.sidebar__search {
  --sidebar-content-padding: 0;
  @include for-desktop {
    .sf-sidebar__aside {
      --sidebar-aside-width: 800px;
    }
  }
}
</style>
