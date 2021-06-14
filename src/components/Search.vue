<template>
  <v-card color="red lighten-2" dark>
    <v-card-title class="text-h5 red lighten-3">
      Search for Public APIs
    </v-card-title>
    <v-card-text>
      Explore hundreds of free API's ready for consumption! For more information
      visit
      <a
        class="grey--text text--lighten-3"
        href="https://github.com/toddmotto/public-apis"
        target="_blank"
        >the GitHub repository</a
      >.
    </v-card-text>
    <v-card-text>
      <v-autocomplete
        v-model="model"
        :items="items"
        :loading="isLoading"
        :search-input.sync="search"
        color="white"
        hide-no-data
        hide-selected
        item-text="Name"
        item-value="API"
        label="Public APIs"
        placeholder="Start typing to Search"
        prepend-icon="mdi-database-search"
        return-object
        cache-items
      ></v-autocomplete>
    </v-card-text>
    <v-divider></v-divider>
    <v-expand-transition>
      <v-list v-if="model" class="red lighten-3">
        <v-list-item v-for="(field, i) in fields" :key="i">
          <v-list-item-content>
            <v-list-item-title v-text="field.value"></v-list-item-title>
            <v-list-item-subtitle v-text="field.key"></v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-expand-transition>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn :disabled="!model" color="grey darken-3" @click="model = null">
        Clear
        <v-icon right> mdi-close-circle </v-icon>
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  name: "Search",

  data: () => ({
    nameLimit: 60,
    entries: [],
    isLoading: false,
    model: null,
    search: null,
  }),

  computed: {
    fields() {
      if (!this.model) return [];

      return Object.keys(this.model).map((key) => {
        return {
          key,
          value: this.model[key] || "n/a",
        };
      });
    },
    items() {
      console.log(this.entries);
      return this.entries.map((entry) => {
        console.log("entry", entry);
        const Name =
          entry.name.length > this.nameLimit
            ? entry.name.slice(0, this.nameLimit) + "..."
            : entry.name;

        return Object.assign({}, entry, { Name });
      });
    },
  },

  watch: {
    search(val) {
      if (!val || val.length < 3) {
        return;
      }

      this.clearEntries();
      this.isLoading = true;
      this.fetchEntriesDebounced(val);
    },
  },

  methods: {
    clearEntries() {
      this.count = 0;
      this.entries = [];
    },
    fetchEntriesDebounced(val) {
      clearTimeout(this._searchTimerId);
      this._searchTimerId = setTimeout(() => {
        this.fetchEntries(val);
      }, 500);
    },
    fetchEntries(val) {
      const mtg = require("mtgsdk");
      mtg.card
        .where({ name: val })
        .then((res) => {
          console.log(res);
          const cards = res;
          this.entries = cards;
          console.log("test", this.entries);
        })
        .catch((err) => {
          console.log(err);
        })
        .finally(() => (this.isLoading = false));
    },
  },
};
</script>
