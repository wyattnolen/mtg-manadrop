<template>
  <div class="">
    <h1 style="text-align: center;">Datatable with 3rd Party API</h1>
    <v-data-table
      :page="page"
      :items-per-page = "175"
      
      :headers="headers"
      :items="entries"
      :options.sync="options"
      :server-items-length="totalCards"
      :loading="isLoading"
      class="elevation-1"
    >
      <template v-slot:item.logo="{ item }">
        <img :src="item.airline.logo" style="width: 10%;" />
      </template>
      <template v-slot:item.website="{ item }">
        <a :href="item.airline.website">{{ item.airline.website }}</a>
      </template>
    </v-data-table>
  </div>
</template>


<script>
export default {
  name: "TableSearch",
  data() {
    return {
      page: 1,
      totalCards: 0,
      passengers: [],
      isLoading: true,
      entries: [],
      options: {},
      search: "*",
      headers: [
        { text: 'Name', value: 'name' },
      ],
    };
  },
  //this one will populate new data set when user changes current page. 
  watch: {
    options: {
      handler() {
        this.readDataFromAPI();
      },
    },
    deep: true,
  },
  methods: {
    //Reading data from API method. 
    readDataFromAPI() {
      this.isLoading = true;
      const { page } = this.options;
      let pageNumber = page - 1;
      this.axios
        .get(`https://api.scryfall.com/cards/search?q=${this.search}&page=${pageNumber}`)
        .then((response) => {
          //Then injecting the result to datatable parameters.
          this.isLoading = false;
          this.entries = response.data.data;
          this.totalCards = response.data.total_cards
        });
    },
  },
  //this will trigger in the onReady State
  mounted() {
    this.readDataFromAPI();
  },
};
</script>
