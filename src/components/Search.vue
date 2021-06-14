<template>
  <div>
    <!-- <v-card color="red lighten-2" dark>
      <v-card-title class="text-h5 red lighten-3">
        Search for Public APIs
      </v-card-title>
      <v-card-text>
        Explore hundreds of free API's ready for consumption! For more
        information visit
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
    </v-card> -->
    <v-btn
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
          <!-- <template v-slot:selection="data">
            <v-chip
              v-bind="data.attrs"
              :input-value="data.selected"
              close
              @click="data.select"
              @click:close="remove(data.item)"
            >
              <v-avatar left>
                <v-img :src="data.item.avatar"></v-img>
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
                 <img :src="data.item.avatar" />
              </v-list-item-avatar>
              <v-list-item-content>
                <v-list-item-title v-html="data.item.name"></v-list-item-title>
                <v-list-item-subtitle
                  v-html="data.item.group"
                ></v-list-item-subtitle>
              </v-list-item-content>
            </template>
          </template> -->
        </v-autocomplete>
        {{ model.id }}
        <!-- <v-list v-if="model" class="red lighten-3">
          <v-list-item v-for="(item, i) in model" :key="i">
           
            <v-list-item-content>
              <v-list-item-title v-text="item.name"></v-list-item-title>
              <p @click:close="remove(item)">remove</p>
            </v-list-item-content>
          </v-list-item>
        </v-list> -->
        <p @click="submit">test</p>
      </v-container>
    </v-form>
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
    model: { name: "", id: "" },
    search: null,
    selected: [],
  }),

  computed: {
    // fields() {
    //   if (!this.model) return [];
    //   return Object.keys(this.model).map((key) => {
    //     return {
    //       key,
    //       value: this.model[key] || "n/a",
    //     };
    //   });
    // },
    // items() {
    //   return this.entries.map((entry) => {
    //     const Name =
    //       entry.name.length > this.nameLimit
    //         ? entry.name.slice(0, this.nameLimit) + "..."
    //         : entry.name;
    //     return Object.assign({}, entry, { Name });
    //   });
    // },
  },

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

  methods: {
    submit() {
      console.log(this.model);
    },
    remove(item) {
      console.log(item);
      const index = this.model.indexOf(item);
      if (index >= 0) this.model.splice(index, 1);
    },

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
          const cards = res;
          this.entries = cards;
        })
        .catch((err) => {
          console.log(err);
        })
        .finally(() => (this.isLoading = false));
    },
  },
};
</script>
