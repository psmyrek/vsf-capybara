<template>
  <div class="m-error error">
    <SfImage
      src="/assets/error.svg"
      class="error__image"
    />
    <SfHeading :level="level" :title="title" :subtitle="subtitle" class="error__message" />
    <div class="error__action">
      <router-link :to="localizedRoute('/')">
        <SfButton class="sf-button--full-width error__action-button">
          {{ $t("Return to home") }}
        </SfButton>
      </router-link>
      <SfButton
        class="sf-button--full-width sf-button--text error__action-button error__action-button--secondary"
        @click="$router.back()"
      >
        {{ $t("Back") }}
      </SfButton>
    </div>
  </div>
</template>

<script>
import { SfButton, SfImage, SfHeading } from '@storefront-ui/vue';

export default {
  name: 'MError',
  components: { SfButton, SfImage, SfHeading },
  props: {
    level: {
      type: Number,
      default: 1
    },
    title: {
      type: String,
      default: ''
    },
    subtitle: {
      type: String,
      default: ''
    }
  }
};
</script>
<style lang="scss" scoped>
@import "~@storefront-ui/vue/styles";

.error {
  margin: auto;
  padding: 0 1.25rem;
  &__image,
  &__message {
    margin: calc(var(--spacer-extra-big) * 3) 0;
    text-align: center;
  }
  &__image {
    display: flex;
    justify-content: center;
  }
  &__action {
    max-width: 24rem;
    margin: var(--spacer-extra-big) auto;
    @include for-desktop {
      display: flex;
    }
  }
  &__action-button {
    flex: 1;
    &--secondary {
      margin: var(--spacer-big) 0;
      @include for-desktop {
        margin: 0;
        text-align: right;
      }
    }
  }
}
</style>
