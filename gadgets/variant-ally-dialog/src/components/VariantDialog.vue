<script setup lang="ts">
import { ref, watchEffect } from 'vue';
import useI18n, { currentLang, cycleThroughLangs } from '../composables/useI18n';
import LangSwitchTransition from './LangSwitchTransition.vue';
import ExpandTransition from './ExpandTransition.vue';
import VariantButton from './VariantButton.vue';
import IconButton from './IconButton.vue';
import useUniqueId from '../composables/useUniqueId';
import useSyncedRef from '../composables/useSyncedRef';

const VARIANTS = ['cn', 'sg', 'my', 'hk', 'mo', 'tw'];

const dialog = ref<HTMLElement | null>(null);
const focusKeeper = ref<HTMLElement | null>(null);

const titleId = useUniqueId();
const descId = useUniqueId();
const isDescExtended = useSyncedRef('ext', false);

const props = defineProps<{
  open: boolean,
}>();
const emit = defineEmits<{
  (e: 'select', variant: string): void;
  (e: 'update:open', value: boolean): void;
}>();

watchEffect(() => {
  if (props.open) {
    focusKeeper.value?.focus();
  }
});

/**
 * Focus the first or last focusable element inside the dialog.
 * @param last focus last element, first element otherwise
 */
function focusInsideDialog(last: boolean) {
  if (dialog.value === null) {
    return;
  }
  let focusableElements: HTMLElement[] = Array.from(dialog.value.querySelectorAll('button, a'));
  if (last) {
    focusableElements = focusableElements.reverse();
  }
  for (const element of focusableElements) {
    // Try focus the element, may fail
    element.focus();

    if (document.activeElement === element) {
      return;
    }
  }
}

function close() {
  emit('update:open', false);
}

</script>

<template>
  <Transition
    name="va-dialog"
    appear
  >
    <div
      v-if="open"
      class="va-dialog-backdrop"
      @click="close"
      @keyup.escape="close"
    >
      <div
        ref="dialog"
        class="va-dialog"
        role="dialog"
        aria-model="false"
        :aria-labelledby="titleId"
        :aria-describedby="descId"
        @click.stop
      >
        <!-- Focus trap start & keeper -->
        <div
          tabindex="0"
          @focus="focusInsideDialog(true)"
        />
        <div
          ref="focusKeeper"
          class="va-focus-keeper"
          tabindex="-1"
        />

        <div class="va-dialog__header">
          <LangSwitchTransition>
            <h2
              :id="titleId"
              :key="currentLang"
              :lang="currentLang"
              class="va-title va-dialog__header__title"
            >
              {{ useI18n('title') }}
            </h2>
          </LangSwitchTransition>
          <IconButton
            class="va-dialog__header__lang-button"
            icon="lang"
            title="切换语言 / 切換語言 / Switch languages"
            aria-label="切换语言 / 切換語言 / Switch languages"
            @click="cycleThroughLangs"
          />
          <IconButton
            class="va-dialog__header__close-button"
            icon="close"
            :title="useI18n('close')"
            :aria-label="useI18n('close')"
            @click="close"
          />
        </div>

        <LangSwitchTransition>
          <div
            :key="currentLang"
            class="va-dialog__body"
            :lang="currentLang"
          >
            <p
              :id="descId"
              class="va-para va-dialog__body__desc"
            >
              {{ useI18n('desc') }}{{ useI18n('space') }}<a
                class="va-link"
                href="#"
                @click.prevent="isDescExtended = !isDescExtended"
              >{{ useI18n('desc.btn') }}</a>
            </p>
            <ExpandTransition :expand="isDescExtended">
              <div class="va-dialog__body__ext">
                <p class="va-para va-para--subtle">
                  {{ useI18n('desc.ext.1') }}
                </p>
                <p class="va-para va-para--subtle">
                  {{ useI18n('desc.ext.2') }}
                </p>
                <p class="va-para va-para--subtle">
                  {{ useI18n('desc.ext.3') }}
                </p>
              </div>
            </ExpandTransition>

            <div
              class="va-dialog__body__opts"
              :class="{ 'va-dialog__body__opts--shrunk': isDescExtended }"
            >
              <VariantButton
                v-for="variant in VARIANTS"
                :key="variant"
                :lang="currentLang === 'en' ? 'en' : `zh-${variant}`"
                @click="$emit('select', `zh-${variant}`)"
              >
                {{ useI18n(`vb.${variant}`) }}
              </VariantButton>
            </div>
          </div>
        </LangSwitchTransition>

        <LangSwitchTransition>
          <footer
            :key="currentLang"
            class="va-dialog__footer"
            :lang="currentLang"
          >
            <p class="va-para">
              {{ useI18n('footer.1') }}
            </p>
            <p class="va-para">
              <!-- Disable link because it's unfinished -->
              {{ useI18n('footer.2') }}{{ useI18n('space')
              }}<!-- <a
                class="va-link"
                href="#"
              >{{ useI18n('footer.2.btn') }}</a> -->
            </p>
          </footer>
        </LangSwitchTransition>

        <!-- Focus trap end -->
        <div
          tabindex="0"
          @focus="focusInsideDialog(false)"
        />
      </div>
    </div>
  </Transition>
</template>

<style scoped lang="less">
@import (reference) '../styles/mixins.less';
@import (reference) '../styles/tokens.less';

.va-link {
  .link-base();
}

.va-title {
  border: 0; // Reset
  padding: 0; // Reset
  overflow-wrap: break-word;

  font-size: @font-size-xx-large;
  font-weight: @font-weight-bold;
  margin: @spacing-75 @spacing-0;
  color: @color-base;
}

.va-para {
  overflow-wrap: break-word;
  margin-top: @spacing-0;
  margin-bottom: @spacing-75;
  color: @color-base;

  &--subtle {
    color: @color-subtle;
    font-size: @font-size-small;
    margin-bottom: @spacing-35;
  }
}

.va-dialog-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;

  z-index: @z-index-overlay-backdrop;
  background-color: @background-color-backdrop-light;

  display: flex;
  align-items: center;
  justify-content: center;

  @media (prefers-reduced-motion) {

    &,
    & :deep(*) {
      transition-duration: 0s !important;
    }
  }
}

.va-focus-keeper {
  position: absolute;

  &:focus {
    outline: 0;
  }
}

.va-dialog {
  box-sizing: border-box;
  z-index: @z-index-overlay;
  padding: @spacing-125 @spacing-200;
  width: calc(100% - 2em);
  max-width: 42em;
  max-height: calc(100vh - 2em);

  overflow: auto;
  overscroll-behavior: none;
  background-color: @background-color-base;
  border: @border-base;
  border-radius: @border-radius-base;
  box-shadow: @box-shadow-drop-medium;

  font-family: @font-family-system-sans;
  font-size: 1rem; // Reset

  &__header {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    justify-content: center;
    margin-right: -(@spacing-horizontal-button-icon-only + 1px);

    &__title {
      flex: 1;
    }

    &__lang-button {
      order: 1;
    }

    &__close-button {
      order: 3;
    }
  }

  &__body {
    &__desc {
      margin-bottom: @spacing-0;
    }

    &__ext {
      margin-top: @spacing-75;
    }

    &__opts {
      display: grid;
      gap: @spacing-35;
      grid-auto-flow: column;
      grid-template: 1fr 1fr 1fr / 1fr 1fr;
      margin: @spacing-100 0;
    }
  }


  @media screen and (max-width: @max-width-breakpoint-mobile) {
    padding: @spacing-125;

    &__header {
      margin-left: -(@spacing-horizontal-button-icon-only + 1px);

      &__title {
        font-size: @font-size-large;
        text-align: center;
        order: 2;
      }
    }

    &__body__opts {
      grid-auto-flow: row;
      grid-template: repeat(2, minmax(@size-variant-button--mobile, 1fr)) / 1fr 1fr 1fr;

      transition-property: @transition-property-layout;
      transition-duration: @transition-duration-medium;
      transition-timing-function: @transition-timing-function-system;

      &--shrunk {
        grid-template: repeat(2, minmax(@size-variant-button--mobile-shrunk, 1fr)) / 1fr 1fr 1fr;
      }
    }
  }
}

/* Dialog transition effect */
.va-dialog-enter-active,
.va-dialog-leave-active {
  transition-property: @transition-property-fade, @transition-property-layout;
  transition-duration: @transition-duration-medium;
  transition-timing-function: @transition-timing-function-system;
}

.va-dialog-enter-from,
.va-dialog-leave-to {
  opacity: 0;
}
</style>
