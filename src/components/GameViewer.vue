<script>
export default {
  name: "GameViewer",
  props: ["selectedItem"]
}
</script>

<template>
  <div class="viewer">
    <iframe sandbox="allow-scripts" v-if="!!selectedItem" :src="'https://www.nintendo.com' + selectedItem.url"></iframe>
    <p v-else>Nothing to display</p>
  </div>
</template>

<style scoped>
.viewer {
  height: 100%;
  background-color: #222222;
  border-right: 0.01em solid #444;
}

iframe {
  width: 100%;
  height: 100%;
  border: none;
  padding: 0;
  margin: 0;
}
</style>