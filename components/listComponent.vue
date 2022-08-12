<template>
  <v-card class="pa-3 mb-2">
    <p>{{ listObj.name }}</p>
    <!-- <p>ID: {{ listObj.id }}</p> -->
    <v-btn icon @click.stop="$emit('delete-list', listObj.id)">
      <v-icon color="red">mdi-delete</v-icon>
    </v-btn>
  </v-card>
</template>
<script>
export default {
  props: ["list-obj"],
};
</script>
