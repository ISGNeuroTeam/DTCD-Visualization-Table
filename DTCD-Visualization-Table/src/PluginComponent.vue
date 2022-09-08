<template>
  <div class="VisualizationTable">
    <div
      v-if="title"
      class="title"
      v-text="title"/>
    <div v-if="dataset.length < 1" class="NoData">
      <span class="FontIcon name_infoCircleOutline Icon"></span>
      Нет данных для отображения
    </div>
    <table v-else class="DataTable">
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
            :style="getCellStyle(row, colName)"
          />
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'PluginComponent',
  data: () => ({
    dataset: [],
    title: '',
    hiddenColumns: [
      'metadata',
    ],
  }),
  computed: {
    headers() {
      if (this.dataset.length < 1) {
        return [];
      }
      return Object.keys(this.dataset[0])
        .filter((name) => !this.hiddenColumns.includes(name))
    },
  },
  methods: {
    setTitle(value = '') {
      this.title = value;
    },

    setDataset(data = []) {
      this.dataset = data;
    },

    /**
     * Генерируем стили для ячейки по метаданным в таблице
     * metadata = "{'имяЯчейкиВСтроке':{'text_color':'red', ... }, ... }"
     * @param {Object} row
     * @param {String} colName
     * @return {null|{Object}}
     */
    getCellStyle(row, colName) {
      if (row.metadata) {
        const styles = new Map();
        const propsToStyles = [
          ['text_color', 'color'],
          ['text_weight',  'font-weight'],
          ['background_color',  'background-color'],
        ];
        try {
          const metadata = JSON.parse(row.metadata.replaceAll("'", '"'));
          if (metadata.hasOwnProperty(colName)) {
            const cellMetadata = metadata[colName];
            propsToStyles.forEach(([key, styleName]) => {
              if (cellMetadata.hasOwnProperty(key)) {
                styles.set(styleName, cellMetadata[key])
              }
            })
            return Object.fromEntries(styles);
          }
        } catch (err) {
          console.warn(err)
        }
      }
      return null;
    }
  },
};
</script>

<style lang="sass" scoped>
*
  box-sizing: border-box
  margin: 0
  padding: 0

.VisualizationTable
  width: 100%
  height: 100%
  overflow: auto
  padding: 10px
  color: var(--text_secondary)
  font-family: 'Proxima Nova', sans-serif
  background-color: var(--background_main)

  .title
    color: var(--text_main)
    font-size: 18px
    font-weight: 700
    line-height: 25px
    padding-bottom: 8px

  .NoData
    height: 100%
    display: flex
    flex-direction: column
    align-items: center
    justify-content: center

    .Icon
      color: var(--border_secondary)
      font-size: 100px
      margin-bottom: 8px

  .DataTable
    width: 100%
    text-align: left
    border-collapse: collapse

    thead
      color: var(--title)
      font-size: 15px
      font-weight: 700

      tr th
        height: 40px
        padding: 0 30px
        background-color: var(--border_24)

    tbody
      color: var(--text_main)

      tr
        height: 30px
        font-size: 13px
        line-height: 16px

        td
          padding: 10px 30px

        &:nth-child(even)
          background-color: var(--border_24)
</style>
