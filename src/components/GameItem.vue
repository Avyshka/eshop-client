<script>
export default {
  name: "GameItem",
  props: ["item", "index", "selectedItem"]
}
</script>

<template>
  <li :class="{selected: item === selectedItem}" v-on:click.prevent="$emit('item-selected', item)">
    <img :src="item.image_url || item.image_url_sq_s" alt="">
    <div class="info">
      <strong>{{ index + 1 }}</strong>
      <p>{{ item.title }}</p>
    </div>
  </li>
</template>

<style scoped>
.info {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

p {
  margin: 0;
  font-size: 0.6em;
  text-align: left;
}

li {
  display: flex;
  justify-content: flex-start;
  padding: 0.2rem;
  align-items: center;
  gap: 0.3em;
  border-bottom: 0.01em solid #444;
}

img {
  width: 64px;
  height: 64px;
}

.selected {
  background-color: #e60012;
  color: white;
}
</style>
