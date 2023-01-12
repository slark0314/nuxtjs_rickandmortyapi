<template>
<div class="container">
    <b-form inline>
        <b-form-input 
            v-model="name" 
            placeholder="Enter the character's name"
            >
        </b-form-input>
        <label for="status" class="ml-2">status</label>
        <b-form-select 
            id="status"
            v-model="selected_status" 
            :options="status_options"
            class="ml-2">
        </b-form-select>
        <label for="gender" class="ml-2">gender</label>
        <b-form-select 
            id="gender"
            v-model="selected_gender" 
            :options="gender_options"
            class="ml-2"
        >
        </b-form-select>
    </b-form>
    <div class="mt-2">
        <b-table
            ref="table"
            @row-hovered="rowHoverHandler"
            @row-unhovered="rowUnHoveredHandler"
            :items="fetchData"
            :fields="fields"
            :current-page="currentPage"
            :per-page="perPage"
            class="m_b_table"
            hover
        >
            <template v-slot:row-details="row">
                <p :id="`row_${row.index}`">xxx</p>
                <b-popover :target="`row_${row.index}`" :show="true" placement="top">
                   <p>{{ row.item.name }}</p>
                   <img :src="row.item.image" class="w-100"/>
                </b-popover>
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
</div>
</template>

<script>
export default {
  components: {
  },
  watch: {
    name(new_value) {
        this.$refs.table.refresh();
    },
    selected_gender(new_value) {
        this.$refs.table.refresh();
    },
    selected_status(new_value) {
        this.$refs.table.refresh();
    },
  },
  data: () => ({
    isBusy: false,
    name: null,
    selected_status: null,
    selected_gender: null,
    status_options: [
        { value: null, text: "all" },
        { value: "alive", text: "alive" },
        { value: "dead", text: "dead" },
        { value: "unknown", text: "unknown" },
    ],
    gender_options: [
        { value: null, text: "all" },
        { value: "female", text: "female" },
        { value: "male", text: "male" },
        { value: "genderless", text: "genderless" },
        { value: "unknown", text: "unknown" },
    ],
    items: [],
    totalRows: 1,
    currentPage: 1,
    perPage: 20,
    fields: [
      { key: 'id', sortable: false },
      { key: 'name', sortable: false },
      { key: 'species', sortable: false },
      { key: 'status', sortable: false },
      { key: 'gender', sortable: false },
      { key: 'origin.name', sortable: false },
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
       console.log('-characters-fetchData-ctx-', ctx)
        try {
            this.isBusy = true
            let params = '?page=' + ctx.currentPage;
            if(this.name) params += `&name=${this.name}`;
            if(this.selected_gender) params += `&gender=${this.selected_gender}`;
            if(this.selected_status) params += `&status=${this.selected_status}`;
            const { data } = await this.$axios.get('https://rickandmortyapi.com/api/character' + params)
            const { results, info } = data
            this.totalRows = info.count
            console.log('-characters-fetchData-data-', data)
            this.isBusy = false
            return results.map(x => { x._showDetails = false; return x; })
        } catch (e) {
            console.log('-characters-fetchData-errror-', e)
            this.isBusy = false
            return []
        }
    },
    async rowHoverHandler(item, index, event) {
        if(!(item._showDetails)) item._showDetails = true;
    },
    rowUnHoveredHandler(item, index, event) {
        setTimeout(() => {
            if(item._showDetails) item._showDetails = false;
        }, 1000);
    },
    test(row) {
        console.log('-characters-fetchData-test-', row)
        return row.value;
    }
  },
}
</script>
