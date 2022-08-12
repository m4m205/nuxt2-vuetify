<template>
  <v-card class="pa-2 mb-2">
    <p>{{ itemObj.name }}</p>
    <p>completed: {{ itemObj.completed }}</p>
  </v-card>
</template>
<script>
export default {
  props: ["item-obj"],
};
</script>
