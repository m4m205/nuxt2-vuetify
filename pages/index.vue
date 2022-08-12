<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="11" md="11">
      <!-- loading view  -->
      <v-card
        v-if="$fetchState.pending"
        class="pa-5 d-flex justify-center outlined tile"
      >
        <v-row justify="center" align="center">
          <v-col cols="12" xs="12" sm="3" md="3">
            <v-skeleton-loader
              v-bind="attrs"
              type="list-item-avatar, divider, card-heading, card-heading, card-heading"
            ></v-skeleton-loader>
          </v-col>
          <v-spacer></v-spacer>
          <v-col cols="12" xs="12" sm="8" md="8">
            <v-skeleton-loader
              v-bind="attrs"
              type="list-item-avatar, divider, card-heading, card-heading, card-heading"
            ></v-skeleton-loader>
          </v-col>
        </v-row>
      </v-card>
      <!-- error message -->
      <v-card
        v-else-if="$fetchState.error"
        class="pa-5 d-flex justify-center outlined tile"
      >
        <v-row justify="center" align="center">
          <v-col cols="12" sm="12" md="12"
            ><v-alert
              dense
              prominent
              type="error"
              border="top"
              color="pink darken-1"
              class="mt-3"
              >An error happened, sorry for inconvenience.</v-alert
            >
          </v-col>
        </v-row>
      </v-card>
      <!-- succuss view -->
      <v-card v-else class="pa-5 d-flex justify-center outlined tile">
        <v-row justify="center" align="center">
          <!-- lists start  -->
          <v-col id="lists-section" cols="12" xs="12" sm="3" md="3">
            <v-form class="px-3">
              <v-text-field
                label="Add new list"
                prepend-icon="mdi-plus"
              ></v-text-field>
            </v-form>
            <list-component v-for="list in lists" :list-obj="list">
            </list-component>
          </v-col>
          <v-spacer></v-spacer>
          <!-- items start  -->
          <v-col id="items-section" cols="12" xs="12" sm="8" md="8">
            <v-form class="px-3">
              <v-text-field
                label="Add new item"
                prepend-icon="mdi-plus"
              ></v-text-field>
            </v-form>
            <item-component v-for="item in items" :item-obj="item">
            </item-component>
          </v-col>
        </v-row>
      </v-card>
    </v-col>
  </v-row>
</template>
<script>
export default {
  props: ["foo"],
  data() {
    return {
      baseURL: "https://todo-api.niveaubepaling.nl/list",
      lists: [],
      items: [],
      selectedListId: null,
    };
  },
  async fetch() {
    const res = await this.$axios.$get(this.baseURL);
    this.lists = res.data;
    this.selectedListId = res.data[0].id;
    this.fetchItems(this.selectedListId);
  },
  fetchOnServer: false,
  fetchKey: "lists-data",
  methods: {
    async fetchItems(id) {
      const res = await this.$axios.$get(`${this.baseURL}/${id}`);
      this.items = res.data.items;
    },
  },
};
</script>

<style>
#lists-section {
  /* background-color: aqua; */
  min-height: 300px;
}
#items-section {
  /* background-color: rgb(255, 0, 200); */
  min-height: 300px;
}
</style>
