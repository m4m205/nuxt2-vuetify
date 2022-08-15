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
              v-bind="attrs"
              type="list-item-avatar, divider, card-heading, card-heading, card-heading"
            ></v-skeleton-loader>
          </v-col>
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
      <v-card v-else class="pa-5 d-flex justify-center outlined tile">
        <v-snackbar
          v-model="showSnackbar"
          timeout="5000"
          color="green accent-4"
          elevation="24"
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
        <v-row justify="center" align="start">
          <!-- lists start  -->
          <v-col id="lists-section" cols="12" xs="12" sm="4" md="4">
            <!-- <v-form v-model="formInput" class="mb-7"> -->
            <v-text-field
              class="mb-7"
              label="Add new list"
              prepend-icon="mdi-plus"
              hide-details="auto"
              v-model.trim="listInput"
              :error-messages="[]"
              v-on:keyup.enter="createList"
            ></v-text-field>
            <!-- </v-form> -->
            <list-component
              v-for="list in lists"
              :list-obj="list"
              :isSelected="list.id === selectedListId"
              @delete-list="PrepareDeleteList"
              @click.native="handleListClick(list.id)"
            >
            </list-component>
          </v-col>
          <!-- items start  -->
          <v-col id="items-section" cols="12" xs="12" sm="8" md="8">
            <!-- <v-form class="mb-7"> -->
            <v-text-field
              class="mb-7"
              label="Add new item"
              prepend-icon="mdi-plus"
              hide-details="auto"
              v-model.trim="itemInput"
              :error-messages="[]"
              v-on:keyup.enter="createItem"
            ></v-text-field>
            <!-- </v-form> -->
            <div v-if="items.length">
              <item-component
                v-for="item in items"
                :item-obj="item"
                @delete-item="PrepareDeleteItem"
              >
              </item-component>
            </div>
            <div v-else class="text-center">
              <p>Empty list!</p>
            </div>
          </v-col>
        </v-row>
      </v-card>
    </v-col>
    <v-row justify="center">
      <v-dialog v-model="showItemOverlay" persistent max-width="290">
        <v-card>
          <v-card-title class="text-h5"> Confirmation </v-card-title>
          <v-card-text
            >Are you sure you want to permanently delete this item?</v-card-text
          >
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="blue darken-1"
              text
              @click="
                () => ((showItemOverlay = false), (itemToBeDeletedId = null))
              "
            >
              No
            </v-btn>
            <v-btn
              color="red darken-1"
              text
              @click="[deleteItem(), (showItemOverlay = false)]"
            >
              Yes
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-row>
    <v-row justify="center">
      <v-dialog v-model="showListOverlay" persistent max-width="290">
        <v-card>
          <v-card-title class="text-h5"> Confirmation </v-card-title>
          <v-card-text
            >Are you sure you want to permanently delete this list?</v-card-text
          >
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="blue darken-1"
              text
              @click="
                () => ((showListOverlay = false), (listToBeDeletedId = null))
              "
            >
              No
            </v-btn>
            <v-btn
              color="red darken-1"
              text
              @click="[deleteList(), (showListOverlay = false)]"
            >
              Yes
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-row>
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
      litsToBeDeletedId: null,
      itemToBeDeletedId: null,
      listInput: "",
      itemInput: "",
      showSnackbar: false,
      showItemOverlay: false,
      showListOverlay: false,
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
    async fetchItems(id) {
      const res = await this.$axios.$get(`${this.baseURL}/${id}`);
      this.items = res.data.items;
    },
    async createList() {
      try {
        await this.$axios.$post(this.baseURL, {
          name: this.listInput,
        });
        this.listInput = "";
        this.showSnackbar = true;
        this.$fetch();
      } catch (e) {
        console.log("error from create list", e);
      }
    },
    async createItem() {
      try {
        await this.$axios.$post(`${this.baseURL}/${this.selectedListId}`, {
          name: this.itemInput,
        });
        this.itemInput = "";
        this.showSnackbar = false;
        this.$fetch();
      } catch (e) {
        console.log("error from create item", e);
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
    handleListClick(id) {
      this.selectedListId = id;
      this.fetchItems(id);
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
