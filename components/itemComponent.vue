<template>
  <div>
    <v-card class="pa-2 mb-2" shaped>
      <v-card-actions>
        <!-- <v-row align="center" class="ml-2">
          <v-checkbox
            v-model="itemObj.completed"
            hide-details
            class="shrink mr-2 mt-0"
          ></v-checkbox>
          <v-input>
            <input
              :class="{ 'text-decoration-line-through': itemObj.completed }"
              :value="itemObj.name"
              @change="
                (e) => {
                  $emit('inputValue', e.target.value), $refs.textInput.blur();
                }
              "
              ref="textInput"
              type="text"
            />
          </v-input>
        </v-row> -->
        <v-row align="center" class="ml-2">
          <v-checkbox
            v-model="isCompleted"
            color="success"
            hide-details
            class="shrink mr-2 mt-0"
            @change="updateItem({ keyName: 'completed' })"
          ></v-checkbox>
          <v-text-field
            :class="{ 'line-through': isCompleted }"
            v-model="inputText"
            v-on:keyup.enter="updateItem({ keyName: 'name' })"
            :loading="isLoading"
          ></v-text-field>
        </v-row>
        <v-spacer></v-spacer>
        <v-divider vertical></v-divider>
        <div>
          <v-dialog v-model="auditDialog" max-width="290">
            <template v-slot:activator="{ on, attrs }">
              <v-btn icon @click.stop="" v-bind="attrs" v-on="on">
                <v-icon color="blue">mdi-information-outline</v-icon>
              </v-btn>
            </template>
            <v-card>
              <v-card-title class="text-h5"> Audit trail details </v-card-title>
              <v-card-text>
                <p>created at :{{ formatCreatedDate }}</p>
                <p>updated at : {{ formatUpdatedDate }}</p>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="green darken-1" text @click="auditDialog = false">
                  Okay!
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-btn icon @click.stop="$emit('delete-item', itemObj.id)">
            <v-icon color="red">mdi-delete</v-icon>
          </v-btn>
        </div>
      </v-card-actions>
    </v-card>
  </div>
</template>
<script>
export default {
  props: {
    itemObj: Object,
    listId: Number,
  },
  data() {
    return {
      auditDialog: false,
      isCompleted: undefined,
      inputText: "",
      isLoading: false,
    };
  },
  mounted() {
    this.inputText = this.itemObj.name;
    this.isCompleted = this.itemObj.completed;
  },
  methods: {
    async updateItem({ keyName }) {
      if (keyName === "name" && this.inputText.trim() === this.itemObj.name)
        return;
      try {
        this.isLoading = true;
        const calledFromValue =
          keyName === "name" ? this.inputText : this.isCompleted;
        await this.$axios.$patch(
          `https://todo-api.niveaubepaling.nl/list/${this.listId}/${this.itemObj.id}`,
          { [keyName]: calledFromValue }
        );
      } catch (e) {
        console.log("error from update error API", e);
      } finally {
        setTimeout(() => (this.isLoading = false), 1000);
      }
    },
  },
  computed: {
    formatCreatedDate: function () {
      return this.itemObj.created_at.substring(
        0,
        this.itemObj.created_at.indexOf(".")
      );
    },
    formatUpdatedDate: function () {
      return this.itemObj.updated_at.substring(
        0,
        this.itemObj.updated_at.indexOf(".")
      );
    },
  },
};
</script>
<style>
.line-through {
  text-decoration: line-through;
}
</style>
