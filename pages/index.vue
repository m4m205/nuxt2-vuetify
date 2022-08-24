<template>
  <v-row justify="center" align="start">
    <v-col cols="12" sm="11" md="11">
      <!-- loading view  -->
      <v-card
        v-if="$fetchState.pending && !lists.length"
        class="pa-5 d-flex justify-center outlined tile"
      >
        <v-row justify="center" align="start">
          <v-col cols="12" xs="12" sm="4" md="4">
            <v-skeleton-loader
              elevation="2"
              type="list-item-avatar, divider, card-heading, card-heading, card-heading"
            ></v-skeleton-loader>
          </v-col>
          <v-col cols="12" xs="12" sm="8" md="8">
            <v-skeleton-loader
              elevation="2"
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
        <v-row justify="center" align="start">
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
      <v-card
        v-else
        class="pa-5 d-flex justify-center outlined tile"
        :color="$vuetify.theme.dark ? 'grey darken-3' : 'grey lighten-3'"
      >
        <v-snackbar
          v-model="showSnackbar"
          timeout="4000"
          color="green accent-4"
          elevation="24"
          top
        >
          Successfully created!
          <template v-slot:action="{ attrs }">
            <v-btn
              color="white"
              text
              v-bind="attrs"
              @click="showSnackbar = false"
            >
              Close
            </v-btn>
          </template>
        </v-snackbar>
        <v-row justify="center" align="start" class="view-min-height">
          <!-- lists start  -->
          <v-col cols="12" xs="12" sm="4" md="4">
            <v-form @submit.prevent="">
              <v-text-field
                class="mb-7"
                label="Add new list"
                prepend-icon="mdi-plus"
                hide-details="auto"
                v-model.trim="listInput"
                :error-messages="[]"
                v-on:keyup.enter="createList"
                :loading="listCallIsLoading"
              ></v-text-field>
            </v-form>
            <list-component
              v-for="list in lists"
              :list-obj="list"
              :isSelected="list.id === selectedListId"
              @delete-list="PrepareDeleteList"
              @click.native="handleListClick(list.id)"
            >
            </list-component>
          </v-col>
          <v-col v-if="fetchingItems" cols="12" xs="12" sm="8" md="8">
            <v-skeleton-loader
              elevation="2"
              type="list-item-avatar, divider, card-heading, card-heading, card-heading"
            ></v-skeleton-loader>
          </v-col>
          <!-- items start  -->
          <v-col v-else cols="12" xs="12" sm="8" md="8">
            <v-form @submit.prevent="">
              <v-text-field
                class="mb-7"
                label="Add new item"
                prepend-icon="mdi-plus"
                hide-details="auto"
                v-model.trim="itemInput"
                :error-messages="[]"
                v-on:keyup.enter="createItem"
                :loading="itemCallIsLoading"
              ></v-text-field>
            </v-form>
            <item-component
              v-for="item in items"
              :item-obj="item"
              :is-loading="updateItemIsLoadingId === item.id"
              @delete-item="PrepareDeleteItem"
              @update-item="updateItem"
            >
            </item-component>
          </v-col>
        </v-row>
      </v-card>
    </v-col>
    <confirmation-message
      :message="itemName"
      :show-overlay="showItemOverlay"
      @negative-clicked="
        () => ((showItemOverlay = false), (itemToBeDeletedId = null))
      "
      @positive-clicked="[deleteItem(), (showItemOverlay = false)]"
    >
    </confirmation-message>
    <confirmation-message
      :message="listName"
      :show-overlay="showListOverlay"
      @negative-clicked="
        () => ((showListOverlay = false), (listToBeDeletedId = null))
      "
      @positive-clicked="[deleteList(), (showListOverlay = false)]"
    >
    </confirmation-message>
  </v-row>
</template>
<script>
export default {
  data() {
    return {
      // baseURL: "https://todo-api.niveaubepaling.nl/list",
      // baseURL: "http://localhost:8000/api/list",
      baseURL: "https://laravelfirstone.azurewebsites.net/api/list",
      lists: [],
      items: [],
      selectedListId: null,
      listToBeDeletedId: null,
      itemToBeDeletedId: null,
      listInput: "",
      itemInput: "",
      listCallIsLoading: false,
      itemCallIsLoading: false,
      updateItemIsLoadingId: null,
      showSnackbar: false,
      showItemOverlay: false,
      showListOverlay: false,
      fetchingItems: false,
    };
  },
  async fetch() {
    const res = await this.$axios.$get(this.baseURL);
    this.lists = res.data;
    this.selectedListId = this.selectedListId
      ? this.selectedListId
      : res.data[0].id;
    this.fetchItems(this.selectedListId);
  },
  fetchOnServer: false,
  fetchKey: "lists-data",
  methods: {
    async fetchItems(id, changeList) {
      try {
        if (changeList) {
          this.fetchingItems = true;
        }
        const res = await this.$axios.$get(`${this.baseURL}/${id}`);
        this.items = res.data.items;
      } catch (e) {
        console.log("error from fetch items", e);
      } finally {
        if (changeList) {
          setTimeout(() => {
            this.fetchingItems = false;
          }, 100);
        }
      }
    },
    async createList() {
      try {
        this.listCallIsLoading = true;
        const res = await this.$axios.$post(this.baseURL, {
          name: this.listInput,
        });
        this.selectedListId = res.data.id;
        this.listInput = "";
        this.showSnackbar = true;
        this.$fetch();
      } catch (e) {
        console.log("error from create list", e);
      } finally {
        setTimeout(() => (this.listCallIsLoading = false), 1000);
      }
    },
    async createItem() {
      try {
        this.itemCallIsLoading = true;
        await this.$axios.$post(`${this.baseURL}/${this.selectedListId}`, {
          name: this.itemInput,
        });
        this.itemInput = "";
        this.showSnackbar = true;
        this.$fetch();
      } catch (e) {
        console.log("error from create item", e);
      } finally {
        setTimeout(() => (this.itemCallIsLoading = false), 1000);
      }
    },
    async deleteList() {
      try {
        await this.$axios.$delete(`${this.baseURL}/${this.listToBeDeletedId}`);
        this.selectedListId = null;
        this.$fetch();
      } catch (e) {
        console.log("error from delete list API", e);
      } finally {
        this.listToBeDeletedId = null;
      }
    },
    async deleteItem() {
      if (!this.itemToBeDeletedId) return new Error("error in deleting item");
      try {
        await this.$axios.$delete(
          `${this.baseURL}/${this.selectedListId}/${this.itemToBeDeletedId}`
        );
        this.fetchItems(this.selectedListId);
      } catch (e) {
        console.log("error from delete error API", e);
      } finally {
        this.itemToBeDeletedId = null;
      }
    },
    PrepareDeleteItem(id) {
      this.itemToBeDeletedId = id;
      this.showItemOverlay = true;
    },
    PrepareDeleteList(id) {
      this.listToBeDeletedId = id;
      this.showListOverlay = true;
    },
    async updateItem({ id, newValue }) {
      try {
        this.updateItemIsLoadingId = id;
        await this.$axios.$patch(
          `${this.baseURL}/${this.selectedListId}/${id}`,
          newValue
        );
        this.fetchItems(this.selectedListId);
      } catch (e) {
        console.log("error from update error API", e);
      } finally {
        setTimeout(() => (this.updateItemIsLoadingId = null), 500);
      }
    },
    handleListClick(id) {
      if (this.selectedListId == id) return;
      this.items = [];
      this.selectedListId = id;
      this.fetchItems(id, true);
    },
  },
  computed: {
    listName() {
      if (!this.listToBeDeletedId) return null;
      let msg = this.lists.find((list) => list.id == this.listToBeDeletedId);
      return `( ${msg.name} ) list`;
    },
    itemName() {
      if (!this.itemToBeDeletedId) return null;
      let msg = this.items.find((item) => item.id == this.itemToBeDeletedId);
      return `( ${msg.name} ) item`;
    },
  },
};
</script>

<style>
.view-min-height {
  min-height: 75vmin;
}
</style>
