<template>
  <v-alert
    :border="isSelected ? 'left' : undefined"
    colored-border
    color="blue"
    elevation="5"
    class="list-card"
    @click=""
  >
    <p>{{ listObj.name }}</p>
    <v-btn icon @click.stop="$emit('delete-list', listObj.id)">
      <v-icon color="red">mdi-delete</v-icon>
    </v-btn>
  </v-alert>
</template>
<script>
export default {
  props: {
    "list-obj": Object,
    isSelected: Boolean,
  },
};
</script>
<style>
.list-card:hover {
  cursor: pointer;
}
</style>
