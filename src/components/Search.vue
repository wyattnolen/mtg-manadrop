<template>
  <div>
    <!-- <v-btn
      :disabled="autoUpdate"
      :loading="isUpdating"
      color="blue-grey darken-3"
      depressed
      @click="isUpdating = true"
    >
      <v-icon left> mdi-update </v-icon>
      Update Now
    </v-btn>
    <v-form>
      <v-container>
        <v-autocomplete
          v-model="model"
          :loading="isLoading"
          :disabled="isUpdating"
          :items="entries"
          :search-input.sync="search"
          filled
          chips
          color="blue-grey lighten-2"
          label="Select"
          item-text="name"
          item-value="id"
          multiple
          return-object
          cache-items
        >
          <template v-slot:selection="data">
            <v-chip
              v-bind="data.attrs"
              :input-value="data.selected"
              close
              @click="data.select"
              @click:close="remove(data.item)"
            >
              <v-avatar left>
                <v-img :src="data.item.imageUrl"></v-img>
              </v-avatar>
              {{ data.item.name }}
            </v-chip>
          </template>
          <template v-slot:item="data">
            <template v-if="typeof data.item !== 'object'">
              <v-list-item-content v-text="data.item"></v-list-item-content>
            </template>
            <template v-else>
              <v-list-item-avatar>
                <v-img :src="data.item.imageUrl"></v-img>
              </v-list-item-avatar>
              <v-list-item-content>
                <v-list-item-title v-html="data.item.name"></v-list-item-title>
                <v-list-item-subtitle
                  v-html="data.item.setName"
                ></v-list-item-subtitle>
              </v-list-item-content>
            </template>
          </template>
        </v-autocomplete>
      </v-container>
    </v-form> -->
  </div>
</template>

<script>
export default {
  name: "Search",

  data: () => ({
    autoUpdate: true,
    isUpdating: false,
    nameLimit: 60,
    entries: [],
    isLoading: false,
    model: null,
    search: null,
    selected: [],
  }),

  computed: {},

  watch: {
    isUpdating(val) {
      if (val) {
        setTimeout(() => (this.isUpdating = false), 3000);
      }
    },
    search(val) {
      if (!val || val.length < 3) {
        return;
      }

      this.clearEntries();
      this.isLoading = true;
      this.fetchEntriesDebounced(val);
    },
  },

  mounted() {
    this.fetchEntriesDebounced("goblin");
  },

  methods: {
    remove(item) {
      const index = this.model.indexOf(item);
      if (index >= 0) this.model.splice(index, 1);
    },

    clearEntries() {
      this.entries = [];
    },
    fetchEntriesDebounced(val) {
      clearTimeout(this._searchTimerId);
      this._searchTimerId = setTimeout(() => {
        this.fetchEntries(val);
      }, 500);
    },
    fetchEntries(val) {
      console.log(val);
      this.axios
        .get(`https://api.scryfall.com/cards/search?q=${val}+unique%3Aprints`)
        .then(function (response) {
          console.log(response.data.data);
        })
        .catch((err) => {
          console.log(err);
        })
        .finally(() => (this.isLoading = false));
    },
  },
};
</script>
