<script setup lang="ts">
defineEmits(['click']);

const props = defineProps<{
  icon: 'lang' | 'close';
}>();
</script>

<template>
  <button
    class="va-icon-button"
    @click="$emit('click')"
  >
    <svg
      class="va-icon-button__icon"
      xmlns="http://www.w3.org/2000/svg"
      xmlns:xlink="http://www.w3.org/1999/xlink"
      width="20"
      height="20"
      viewBox="0 0 20 20"
      aria-hidden="true"
    >
      <g v-if="props.icon === 'lang'">
        <!-- eslint-disable -->
        <path
          d="M20 18h-1.44a.61.61 0 01-.4-.12.81.81 0 01-.23-.31L17 15h-5l-1 2.54a.77.77 0 01-.22.3.59.59 0 01-.4.14H9l4.55-11.47h1.89zm-3.53-4.31L14.89 9.5a11.62 11.62 0 01-.39-1.24q-.09.37-.19.69l-.19.56-1.58 4.19zm-6.3-1.58a13.43 13.43 0 01-2.91-1.41 11.46 11.46 0 002.81-5.37H12V4H7.31a4 4 0 00-.2-.56C6.87 2.79 6.6 2 6.6 2l-1.47.5s.4.89.6 1.5H0v1.33h2.15A11.23 11.23 0 005 10.7a17.19 17.19 0 01-5 2.1q.56.82.87 1.38a23.28 23.28 0 005.22-2.51 15.64 15.64 0 003.56 1.77zM3.63 5.33h4.91a8.11 8.11 0 01-2.45 4.45 9.11 9.11 0 01-2.46-4.45z"
        />
        <!-- eslint-enable -->
      </g>
      <g v-else>
        <path d="m4.34 2.93 12.73 12.73-1.41 1.41L2.93 4.35z" />
        <path d="M17.07 4.34 4.34 17.07l-1.41-1.41L15.66 2.93z" />
      </g>
    </svg>
  </button>
</template>

<style scoped lang="less">
@import (reference) '../styles/mixins.less';

.va-icon-button {
  .button-base();

  background-color: @background-color-transparent;
  color: @color-base;
  border-color: @border-color-transparent;
  padding: @spacing-shorthand-button-icon-only;

  display: flex;
  align-items: center;

  &__icon {
    fill: currentColor;
  }

  &:hover {
    background-color: @background-color-button-quiet--hover;
  }

  &:active {
    background-color: @background-color-button-quiet--active;
    color: @color-emphasized;
    border-color: @border-color-interactive;
  }

  &:focus {
    outline: @outline-base--focus;

    &:not(:active) {
      border-color: @border-color-progressive--focus;
      box-shadow: @box-shadow-progressive--focus;
    }
  }
}
</style>
