<template>
<div>
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
            :items="filted_items"
            :fields="fields"
            :current-page="currentPage"
            :per-page="perPage"
            class="m_b_table w-100"
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
                :total-rows="filted_items.length"
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
  props: ['item_urls'],
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
    item_ids: [],
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
    this.onInit();
    this.fetchData();
  },
  computed: {
    filted_items() {
        try {
            const obj = this;
            let items = this.items;
            if(this.name) {
                items = this.items.filter(x => x.name.includes(obj.name));
            }
            if(this.selected_status) {
                items = this.items.filter(x => x.status.toLowerCase() == obj.selected_status);
            }
            if(this.selected_gender) {
                items = this.items.filter(x => x.gender.toLowerCase() == obj.selected_gender);
            }
            return items;
        } catch(e) {
            console.log('-Component-Characteres-filted_items-error-', e);
            return [];
        }
    }
  },
  methods: {
    onInit() {
        console.log('-Component-Characteres-fetchData-item_urls-', this.item_urls);
        const ids = this.item_urls.length > 0 ? this.item_urls.map(x => { 
            console.log('-Component-Characteres-fetchData-x-', x);
            const strs = x.split('/'); 
            console.log('-Component-Characteres-fetchData-strs-', strs);
            return strs[strs.length - 1]; 
        }) : [];
        this.item_ids = ids;
    },
    async fetchData() {
        try {
            this.isBusy = true
            const { data } = await this.$axios.get('https://rickandmortyapi.com/api/character/' + this.item_ids.join(','))
            console.log('-characters-fetchData-data-', data)
            this.isBusy = false
            this.items = data.map(x => { x._showDetails = false; return x; })
        } catch (e) {
            console.log('-characters-fetchData-errror-', e)
            this.isBusy = false
        }
    },
    onChangeQuery(queryParams) {
      this.queryParams = queryParams
      this.fetchData()
    },
    rowHoverHandler(item, index, event) {
        if(!(item._showDetails)) item._showDetails = true;
        setTimeout(() => {
            if(item._showDetails) item._showDetails = false;
        }, 1000);
    },
    rowUnHoveredHandler(item, index, event) {
        setTimeout(() => {
            if(item._showDetails) item._showDetails = false;
        }, 1000);
    }
  },
}
</script>
