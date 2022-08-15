<template>
  <v-hover v-slot="{ hover }">
    <v-alert
      :border="isSelected ? 'left' : undefined"
      type="info"
      text
      color="primary"
      :elevation="hover ? 10 : 4"
      class="list-card"
      icon="mdi-list-box"
      prominent
    >
      <!-- <p>{{ listObj.name }}</p>
      <v-btn icon @click.stop="$emit('delete-list', listObj.id)">
        <v-icon color="red">mdi-delete</v-icon>
      </v-btn> -->
      <v-row align="center">
        <v-col class="grow"> {{ listObj.name }} </v-col>
        <v-col class="shrink">
          <v-btn icon @click.stop="$emit('delete-list', listObj.id)">
            <v-icon color="red">mdi-delete</v-icon>
          </v-btn>
        </v-col>
      </v-row>
    </v-alert>
  </v-hover>
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
