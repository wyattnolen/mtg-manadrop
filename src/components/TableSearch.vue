<template>
  <div class="">
    <h1 style="text-align: center">Datatable with 3rd Party API</h1>
    <v-data-table
      :page="page"
      :headers="headers"
      :items="entries"
      :items-per-page="175"
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
      totalCards: 0,
      isLoading: true,
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
