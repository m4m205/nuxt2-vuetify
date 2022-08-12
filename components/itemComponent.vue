<template>
  <v-card class="pa-2 mb-2">
    <p>{{ itemObj.name }}</p>
    <p>completed: {{ itemObj.completed }}</p>

    <v-btn icon @click.stop="">
      <v-icon color="green">mdi-check-circle</v-icon>
    </v-btn>
    <v-btn icon @click.stop="$emit.deleteItem">
      <v-icon color="red">mdi-delete</v-icon>
    </v-btn>
    <v-btn icon @click.stop="">
      <v-icon color="blue">mdi-information-outline</v-icon>
    </v-btn>
  </v-card>
</template>
<script>
export default {
  props: ["item-obj"],
};
</script>
