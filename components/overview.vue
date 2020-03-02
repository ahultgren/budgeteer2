<template>
  <div class="overview box">
    <div
      v-for="item in currentCategories()"
      :key="item.name"
      class="overview-category"
    >
      <span class="overview-name">{{ item.name }}</span>
      <span class="overview-amount">{{ item.amount }}</span>
      <span class="overview-divider">/</span>
      <input v-model="period.budget[item.name]" class="overview-budget" />
    </div>
    <div class="overview-total">
      <span class="overview-name">Total:</span>
      <span class="overview-amount">{{ totalSpent(period) }}</span>
      <span class="overview-divider">/</span>
      <span class="overview-budget">{{ totalBudget(period) }}</span>
    </div>
  </div>
</template>

<script>
import { currentCategories, totalSpent, totalBudget } from '~/assets/scripts'

export default {
  props: ['period'],
  methods: {
    currentCategories() {
      return currentCategories(this.period)
    },
    totalSpent,
    totalBudget
  }
}
</script>

<style lang="less">
.overview {
  &-category,
  &-total {
    display: flex;
  }

  &-category {
    margin-bottom: 10px;
  }

  &-total {
    border-top: 1px solid #ccc;
    margin-top: 17px;
    padding-top: 11px;
  }

  &-amount {
    margin-left: auto;
  }

  &-divider {
    margin: 0 5px;
  }

  &-budget {
    width: 50px;
    padding: 0 0 1px 0;
    border: 0;
    border-bottom: 1px dashed #333;
  }
}
</style>
