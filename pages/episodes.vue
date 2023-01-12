<template>
<div class="container">
    <div>
        <b-table
            ref="table"
            @row-clicked="rowClicked"
            :items="fetchData"
            :fields="fields"
            :current-page="currentPage"
            :per-page="perPage"
        >
            <template v-slot:row-details="data">
                <span v-b-tooltip.hover :title="xxxx">{{ 'xxx' }}</span>
            </template>
        </b-table>
        <div class="d-flex justify-content-center">
            <b-pagination
                v-model="currentPage"
                :total-rows="totalRows"
                :per-page="perPage"
                align="fill"
                size="sm"
                class="my-0"
            ></b-pagination>
        </div>
    </div>
    <b-modal v-if="selected_item" size="lg" v-model="modalShow" hide-footer hide-header>
        <b-form inline>
            <label>name:</label>
            <label class="ml-2">{{ selected_item.name }}</label>
            <label class="ml-4">air_date:</label>
            <label class="ml-2">{{ selected_item.air_date }}</label>
        </b-form>
        <div class="mt-2">
            <Characters :item_urls="selected_item.characters" />
        </div>
    </b-modal>
</div>
</template>

<script>
export default {
  components: {
  },
  watch: {
  },
  data: () => ({
    isBusy: false,
    modalShow: false,
    items: [],
    totalRows: 1,
    currentPage: 1,
    perPage: 20,
    selected_item: null,
    fields: [
      { key: 'id', sortable: false },
      { key: 'name', sortable: false },
    ],
  }),
  mounted() {
    this.onInit({})
  },
  methods: {
    async onInit() {
    },
    onChangeQuery(queryParams) {
      this.queryParams = queryParams
      this.fetchData()
    },
    async fetchData(ctx) {
       console.log('-episodes-fetchData-ctx-', ctx)
        try {
            this.isBusy = true
            let params = '?page=' + ctx.currentPage;
            const { data } = await this.$axios.get('https://rickandmortyapi.com/api/episode' + params)
            const { results, info } = data
            this.totalRows = info.count
            console.log('-episodes-fetchData-data-', data)
            this.isBusy = false
            return results
        } catch (e) {
            console.log('-episodes-fetchData-errror-', e)
            this.isBusy = false
            return []
        }
    },
    rowClicked(item, index, event) {
        this.selected_item = item;
        this.modalShow = true;
    },
  },
}
</script>
