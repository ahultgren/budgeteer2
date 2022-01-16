<template>
  <div class="container">
    <h1>Import data from herkou</h1>
    <iframe src="https://ahbudgeteer.herokuapp.com/budgeteer2/#/export" />

    <div v-if="importedData">
      The following data was found on the old domain. Want to import it?
      <button @click="doImport()">Yes!</button>
      <p>Then go <nuxt-link to="/">here</nuxt-link> to use the app</p>
      <p>{{ importedData }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      importedData: ''
    }
  },
  mounted() {
    console.log('Parent mounted')
    window.addEventListener(
      'message',
      (msg) => {
        console.log('MESSAGE', msg)

        if (msg.data[0] === '[') {
          this.importedData = msg.data
        }
      },
      false
    )
  },
  methods: {
    doImport() {
      localStorage.periods = this.importedData
      console.log('DONE')
    }
  }
}
</script>

<style lang="less"></style>
