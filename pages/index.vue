<template>
  <div class="budgetlist">
    <nuxt-link
      v-for="(period, index) in periods"
      :key="index"
      :to="'/budget/' + index"
      class="budgetlist-item"
      :value="index"
    >
      <span class="budgetlist-item-title">{{ title(period.ledger) }}</span>
      <span class="budgetlist-item-summary"
        >{{ totalSpent(period) }} / {{ totalBudget(period) }}</span
      >
    </nuxt-link>
  </div>
</template>

<script>
import { totalSpent, totalBudget } from './budget/_currentPeriod/scripts'

export default {
  data() {
    return {
      periods: [
        {
          ledger: 'September\n\n10 test',
          budget: {
            test: 20
          }
        }
      ]
    }
  },
  mounted() {
    if (localStorage.periods) {
      try {
        this.periods = JSON.parse(localStorage.periods)
      } catch (e) {
        console.error(e)
      }
    }
  },
  methods: {
    title(ledger) {
      return ledger.split('\n')[0]
    },
    totalSpent,
    totalBudget
  }
}
</script>

<style lang="less">
.budgetlist {
  &-item {
    text-decoration: none;
    color: inherit;
    margin: 0 20px;
    border-bottom: 1px solid #eee;
    display: block;
    padding: 12px 0;

    &-title {
      display: block;
      margin-bottom: 3px;
    }

    &-summary {
      display: block;
      font-size: 14px;
      color: #666;
    }
  }
}
</style>
