<template functional>
  <div class="schedule-transaction">
    <component :is="injections.components.BackButton" />
    <router-view :tokens-with-balance="props.tokensWithBalance" />
  </div>
</template>

<script>
import BackButton from '@/layouts/InterfaceLayout/components/BackButton';

export default {
  inject: {
    components: {
      default: {
        BackButton
      }
    }
  },
  props: {
    tokensWithBalance: {
      type: Array,
      default: function() {
        return [];
      }
    }
  }
};
</script>

<style>
@import 'ScheduleTransaction.scss';
</style>
