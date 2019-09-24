<template>
  <div class="container">
    <div class="nav box">
      <select v-model="currentPeriod">
        <option v-for="(period, index) in periods" :key="index" :value="index">
          {{ title(period.ledger) }}
        </option>
      </select>
      <button class="nav-add btn" @click="addLedger()">+</button>
      <button class="nav-view btn" @click="toggleView()">
        Show {{ view === 'ledger' ? 'budget' : 'ledger' }}
      </button>
      <a class="btn" :href="downloadData()" target="_blank">v</a>
    </div>

    <div v-if="view === 'ledger'" class="ledger">
      <textarea
        v-model.lazy="periods[currentPeriod].ledger"
        class="ledger-input box"
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
      <div class="budget-total">{{ Math.sum(...currentCategories().map(x => x.amount)) }}</div>
    </div>
  </div>
</template>

<script>
import * as R from 'ramda'

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
      currentPeriod: 0
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

    if (Number(localStorage.currentPeriod)) {
      this.currentPeriod = Number(localStorage.currentPeriod)
    }
  },
  methods: {
    title(ledger) {
      return ledger.split('\n')[0]
    },
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
      const period = this.periods[this.currentPeriod]
      const ledger = period.ledger
      const entries = ledger.split(/\n/g).slice(1)
      const currencies = {
        sek: 1
      }
      let currentCurrencyValue = 1

      return R.pipe(
        R.reduce((obj, entry) => {
          const match = entry
            .toLowerCase()
            .match(/^([+-]?(?:[0-9]*[.])?[0-9]+)([a-z]{3})? +([^ ]+) *(.*)$/)

          if (match) {
            const [, amount, currencyCode, name] = match
            const category = (obj[name] = obj[name] || {
              amount: 0,
              budget: period.budget[name] || 0,
              name
            })
            const currencyValue = currencyCode
              ? currencies[currencyCode]
              : currentCurrencyValue
            category.amount += Math.round(Number(amount) / currencyValue)
            return obj
          }

          const currency = entry
            .toLowerCase()
            .match(/^-([a-z]{3}) +([+-]?(?:[0-9]*[.])?[0-9]+)$/)
          if (currency) {
            const [, code, value] = currency
            currentCurrencyValue = currencies[code] = Number(value)
            return obj
          }

          return obj
        }, {}),
        R.values,
        R.sortBy(R.prop('name'))
      )(entries)
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
  height: calc(100vh - 53px);

  &-input {
    width: 100%;
    height: 100%;
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
