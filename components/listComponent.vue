<template>
  <v-card class="pa-3 mb-2">
    <p>{{ listObj.name }}</p>
    <!-- <p>ID: {{ listObj.id }}</p> -->
  </v-card>
</template>
<script>
export default {
  props: ["list-obj"],
};
</script>
