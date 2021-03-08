<template>
  <label
    class="cb-wrapper mr-4"
    :class="{ 'is-checked': isChecked }"
    @click="$emit('click', isChecked)"
  >
    <input class="cb-input" type="checkbox" :checked="isChecked" />
    <span class="cb-text">{{ labelText }}</span>
  </label>
</template>

<script>
export default {
  props: {
    labelText: {
      type: String,
      required: true,
    },
    isChecked: {
      type: Boolean,
    },
  },
}
</script>

<style lang="scss" scoped>
@import '~assets/scss/main.scss';

.cb-wrapper {
  display: inline-flex;
  position: relative;
  align-items: center;

  &::before {
    transition: background-color 0.3s ease;
    background-position: center center;
    background-repeat: no-repeat;
    background-size: 50% 50%;
  }

  .cb-input {
    display: block;
    opacity: 0;
    width: 1em;
    height: 1em;
    position: absolute;
  }

  &::before {
    content: '';
    display: inline-flex;
    width: 1em;
    height: 1em;
    background-color: rgba($light, 0.25);
    border-radius: 4px;
    margin-right: 0.75em;
    cursor: pointer;
  }

  &.is-checked {
    &::before {
      background-color: $success;
      background-image: url("data:image/svg+xml,%3Csvg width='13' height='9' viewBox='0 0 13 9' fill='none' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M1.64478 4.55151L4.89478 7.80151L11.3948 1.30151' stroke='white' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'/%3E%3C/svg%3E%0A");
      background-size: 70% 70%;
    }
  }

  &:hover,
  &:focus {
    cursor: pointer;
    &::before {
      background-color: rgba($green, 0.1);
      background-image: url("data:image/svg+xml,%3Csvg width='13' height='9' viewBox='0 0 13 9' fill='none' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M1.64478 4.55151L4.89478 7.80151L11.3948 1.30151' stroke='white' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'/%3E%3C/svg%3E%0A");
      background-size: 70% 70%;
    }
  }
}
</style>
