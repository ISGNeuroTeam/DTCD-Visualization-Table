<template>
  <div class="visualization-table-container">
    <table class="datatable">
      <thead>
        <tr>
          <th
            v-for="(header, index) in headers"
            :key="`th-${index}`"
            v-text="header"
          />
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, rowIndex) in dataset" :key="`row-${rowIndex}`">
          <td
            v-for="(colName, colIndex) in headers"
            :key="`row-${rowIndex}-col-${colIndex}`"
            v-text="row[colName]"
          />
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'PluginComponent',
  data: (self) => ({
    logSystem: self.$root.logSystem,
    eventSystem: self.$root.eventSystem,
    headers: [],
    dataset: [],
  }),
  mounted() {
    this.createHeaders();
  },
  methods: {
    render() {
      this.createHeaders();
    },

    setDataset(data = []) {
      this.dataset = data;
    },

    createHeaders() {
      this.headers = this.dataset.length <= 0 ? [] : Object.keys(this.dataset[0]);
    },
  },
};
</script>

<style lang="sass">
@import ./styles/component
</style>
