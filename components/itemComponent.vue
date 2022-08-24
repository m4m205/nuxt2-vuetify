<template>
  <div>
    <v-card class="pa-2 mb-2" shaped>
      <v-card-actions>
        <v-row align="center" class="ml-2">
          <v-checkbox
            v-model="isCompleted"
            color="success"
            hide-details
            class="shrink mr-2 mt-0"
            @change="updateCompleted"
          ></v-checkbox>
          <v-form @submit.prevent="">
            <v-text-field
              :class="{ 'text-decoration-line-through': isCompleted }"
              v-model.trim="inputText"
              v-on:keyup.enter="updateName"
              :loading="isLoading"
              hide-details
              flat
              solo
            ></v-text-field>
          </v-form>
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
    isLoading: Boolean,
  },
  data() {
    return {
      auditDialog: false,
      isCompleted: undefined,
      inputText: "",
    };
  },
  mounted() {
    this.inputText = this.itemObj.name;
    this.isCompleted = this.itemObj.completed;
    },
  watch: { 
    itemObj: function(newVal, oldVal) {
        this.isCompleted = this.itemObj.completed;
        this.inputText = this.itemObj.name;
    }
  },
  methods: {
    updateItem(newValue) {
      const obj = {
        id: this.itemObj.id,
        newValue: newValue,
      };
      this.$emit("update-item", obj);
    },
    updateName() {
      if (this.inputText === this.itemObj.name) return;
      const changedValue = {
        name: this.inputText,
      };
      this.updateItem(changedValue);
    },
    updateCompleted() {
      const changedValue = {
        completed: this.isCompleted,
      };
      this.updateItem(changedValue);
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
