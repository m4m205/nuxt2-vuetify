<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="11" md="11">
      <v-card class="pa-5 d-flex justify-center outlined tile">
        <v-row justify="center" align="center">
          <!-- lists start  -->
          <v-col id="lists-section" cols="12" sm="12" md="3">
            <v-form class="px-3">
              <v-text-field
                label="Add new list"
                prepend-icon="mdi-plus"
              ></v-text-field>
            </v-form>
            <list-component v-for="list in lists" :list-obj="list.ki">
            </list-component>
          </v-col>
          <v-spacer></v-spacer>
          <!-- items start  -->
          <v-col id="items-section" cols="12" sm="12" md="8">
            <v-form class="px-3">
              <v-text-field
                label="Add new item"
                prepend-icon="mdi-plus"
              ></v-text-field>
            </v-form>
            <item-component v-for="item in items" :item-obj="item.i">
            </item-component
          ></v-col>
        </v-row>
      </v-card>
    </v-col>
  </v-row>
</template>
<script>
export default {
  props: ["foo"],
  data() {
    return {
      lists: [
        {
          ki: "1sssss",
        },
        {
          ki: "2sssss",
        },
        {
          ki: "3sssss",
        },
        {
          ki: "4sssss",
        },
      ],
      items: [
        {
          i: "1sssss",
        },
        {
          i: "2sssss",
        },
        {
          i: "3sssss",
        },
        {
          i: "4sssss",
        },
      ],
    };
  },
};
</script>

<style>
#lists-section {
  background-color: aqua;
  min-height: 300px;
}
#items-section {
  background-color: rgb(255, 0, 200);
  min-height: 300px;
}
</style>
