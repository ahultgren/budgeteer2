<template>
  <div v-if="periods[currentPeriod]" class="container">
    <div class="nav box">
      <nuxt-link class="btn" to="/">&lt; Budgets</nuxt-link>
      <button class="nav-view btn" @click="toggleView()">
        Show {{ currentView === 'Ledger' ? 'overview' : 'ledger' }}
      </button>
    </div>

    <component :is="currentView" :period="periods[currentPeriod]"></component>
  </div>
</template>

<script>
import Ledger from '~/components/ledger'
import Overview from '~/components/overview'

export default {
  components: {
    Ledger,
    Overview
  },
  data() {
    return {
      currentView: 'Ledger',
      periods: [],
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
    toggleView() {
      this.currentView = this.currentView === 'Ledger' ? 'Overview' : 'Ledger'
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

.box {
  padding: 10px;
}
.btn {
  -moz-appearance: none;
  -webkit-appearance: none;
  background: #fff;
  border: 1px solid #ddd;
  padding: 6px 12px;
  display: inline-block;
  text-decoration: none;
  color: inherit;
  font-family: inherit;
}
</style>
