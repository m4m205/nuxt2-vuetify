<template>
  <v-row justify="center">
    <v-dialog v-model="showOverlay" persistent max-width="290">
      <v-card>
        <v-card-title class="text-h5"> Confirmation </v-card-title>
        <v-card-text
          >Are you sure you want to permanently delete
          {{ message }}?</v-card-text
        >
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="$emit('negative-clicked')">
            No
          </v-btn>
          <v-btn color="red darken-1" text @click="$emit('positive-clicked')">
            Yes
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>
<script>
export default {
  props: {
    showOverlay: Boolean,
    message: String,
  },
};
</script>
