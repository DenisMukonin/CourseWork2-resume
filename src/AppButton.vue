<template>
  <button
      class="btn primary"
      :disabled="disabled"
      @click.prevent="$emit('action')"
  >
    <slot />
  </button>
</template>

<script>
export default {
  emits: {
    'action': null,
  },
  props: {
    disabled: {
      type: Boolean,
      default: false
    }
  }
}
</script>
