<template>
  <div class="container">
    <div class="nav box">
      <button class="nav-add btn" @click="addLedger()">+</button>
      <a class="btn" :href="downloadData()" target="_blank">Backup</a>
    </div>
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
  </div>
</template>

<script>
import { totalSpent, totalBudget } from '~/assets/scripts'

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
    totalBudget,
    addLedger() {
      this.periods.push({
        ledger: 'New Ledger\n',
        budget: {}
      })
      this.currentPeriod = this.periods.length - 1
    },
    downloadData() {
      return `data:application/octet-stream,${encodeURI(
        JSON.stringify(this.periods)
      )}`
    }
  }
}
</script>

<style lang="less">
.budgetlist {
  &-item {
    text-decoration: none;
    color: inherit;
    margin: 0 10px;
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
