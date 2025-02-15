<script>
import GameItem from "@/components/GameItem.vue"

export default {
  name: "GameItemList",
  props: ["list", "selectedItem"],
  components: {
    GameItem
  }
}
</script>

<template>
  <div class="container">
    <ul>
      <GameItem
          v-for="(item, i) of list"
          v-bind:selectedItem="selectedItem"
          v-bind:item="item"
          v-bind:index="i"
          v-bind:key="item.fs_id"
          v-on:item-selected="$emit('item-selected', item)"
      />
    </ul>
  </div>
</template>

<style scoped>
.container {
  height: 100%;
  background-color: #222222;
  overflow-y: scroll;
  scrollbar-color: #3a3a3a #1e1e1e;
}

ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
