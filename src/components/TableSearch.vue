<template>
  <div class="">
    <h1 style="text-align: center">Datatable with 3rd Party API</h1>
    <v-data-table
      :page.sync="page"
      :items-per-page="itemsPerPage"
      :headers="headers"
      :items="entries"
      :sort-by.sync="sortBy"
      :sort-desc.sync="sortDesc"
      :options.sync="options"
      :server-items-length="totalCards"
      :loading="isLoading"
      class="elevation-1"
    >
    </v-data-table>
  </div>
</template>

<script>
export default {
  name: "TableSearch",
  data() {
    return {
      page: 1,
      itemsPerPage: 175,
      totalCards: 0,
      isLoading: true,
      sortBy: "name",
      sortDesc: false,
      entries: [],
      options: {},
      search: "Goblin",
      headers: [{ text: "Name", value: "name" }],
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
      let pageNumber = page;
      this.axios
        .get(
          `https://api.scryfall.com/cards/search?q=${this.search}&page=${pageNumber}`
        )
        .then((response) => {
          //Then injecting the result to datatable parameters.
          this.isLoading = false;
          this.entries = response.data.data;
          this.totalCards = response.data.total_cards;
          console.log(response);
        });
    },
  },
};
</script>
