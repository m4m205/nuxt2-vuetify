<template>
  <div>
    <v-card class="pa-2 mb-2" shaped>
      <p>{{ itemObj.name }}</p>
      <p>completed: {{ itemObj.completed }}</p>

      <v-dialog v-model="dialog" max-width="290">
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
            <v-btn color="green darken-1" text @click="dialog = false">
              Okay!
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-btn icon @click.stop="$emit('delete-item', itemObj.id)">
        <v-icon color="red">mdi-delete</v-icon>
      </v-btn>
    </v-card>
  </div>
</template>
<script>
export default {
  props: {
    itemObj: Object,
    isSelected: Boolean,
  },
  data() {
    return {
      dialog: false,
    };
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
