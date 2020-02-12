<template>
  <div class="container">
    <div class="nav box">
      <nuxt-link to="/">&lt; Budgets</nuxt-link>
      <button class="nav-add btn" @click="addLedger()">+</button>
      <button class="nav-view btn" @click="toggleView()">
        Show {{ view === 'ledger' ? 'budget' : 'ledger' }}
      </button>
      <a class="btn" :href="downloadData()" target="_blank">v</a>
    </div>

    <div v-if="view === 'ledger'" class="ledger">
      <div
        class="ledger-input ledger-input--facade box"
        v-html="linebreaktobr(periods[currentPeriod].ledger)"
      ></div>
      <textarea
        v-model="periods[currentPeriod].ledger"
        class="ledger-input ledger-input--textarea box"
      ></textarea>
    </div>
    <div v-if="view === 'budget'" class="budget box">
      <div v-for="item in currentCategories()" :key="item.name">
        {{ item.name }}: {{ item.amount }} /
        <input
          v-model="periods[currentPeriod].budget[item.name]"
          class="budget-budget"
        />
      </div>
      <div class="budget-total">
        Total:
        {{ totalSpent() }}
        /
        {{ totalBudget() }}
      </div>
    </div>
  </div>
</template>

<script>
import { currentCategories, totalSpent, totalBudget } from './scripts'

export default {
  components: {},
  data() {
    return {
      view: 'ledger',
      periods: [
        {
          ledger: 'September\n\n10 test',
          budget: {
            test: 20
          }
        }
      ],
      currentPeriod: this.$route.params.currentPeriod
    }
  },
  watch: {
    periods: {
      handler(val) {
        localStorage.periods = JSON.stringify(val)
      },
      deep: true
    },
    currentPeriod(val) {
      localStorage.currentPeriod = JSON.stringify(val)
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
    addLedger() {
      this.periods.push({
        ledger: 'New Ledger\n',
        budget: {}
      })
      this.currentPeriod = this.periods.length - 1
    },
    toggleView() {
      this.view = this.view === 'ledger' ? 'budget' : 'ledger'
    },
    currentCategories() {
      return currentCategories(this.periods, this.currentPeriod)
    },
    totalSpent,
    totalBudget,
    downloadData() {
      return `data:application/octet-stream,${encodeURI(
        JSON.stringify(this.periods)
      )}`
    },
    linebreaktobr(text) {
      return text.replace(/\n/g, '<br>') + '&nbsp;'
    }
  }
}
</script>

<style lang="less">
* {
  box-sizing: border-box;
}

body {
  font-size: 16px;
  font-family: monospace;
}
textarea,
input,
button {
  font-size: inherit;
}

.nav {
  height: 53px;

  &-view {
    float: right;
  }
}

.ledger {
  width: 100%;
  position: relative;
  padding-bottom: 300px;

  &-input {
    width: 100%;
    font-size: inherit;
    font-family: inherit;
    word-spacing: inherit;

    &--facade {
      height: auto;
    }
    &--textarea {
      border: 0;
      height: 100%;
      position: absolute;
      top: 0;
      background-color: transparent;
      color: transparent;
      caret-color: #000;
    }
  }
}

.budget {
  &-budget {
    width: 50px;
  }
}

.box {
  padding: 10px;
}
.btn {
  -moz-appearance: none;
  -webkit-appearance: none;
  background: #fff;
  border: 1px solid #ddd;
  padding: 6px 12px;
}
</style>
