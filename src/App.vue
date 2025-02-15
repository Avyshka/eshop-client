<template>
  <div id="app">
    <div class="top-panel">
      <div class="offline">offline</div>
      <select v-model="filter">
        <option value="all">All</option>
        <option value="wish">Wish</option>
        <option value="black">Black</option>
      </select>
      <button v-if="selectedItem && filter === 'all'" v-on:click="toBlackList">To black list</button>
      <button v-if="selectedItem && filter === 'all'" v-on:click="toWishList">To wish list</button>
      <button v-if="selectedItem && filter !== 'all'" v-on:click="toAllList">Remove from list</button>
    </div>

    <div class="wrapper">
      <GameViewer
          v-bind:selectedItem="selectedItem"
      />
      <GameItemsList
          v-bind:list="filteredList"
          v-bind:selectedItem="selectedItem"
          v-on:item-selected="itemSelected"
      />
    </div>
  </div>
</template>

<script>
import axios from "axios";
import GameItemsList from "@/components/GameItemsList.vue";
import GameViewer from "@/components/GameViewer.vue";
import select from "./assets/select.json";

export default {
  name: "App",
  data() {
    return {
      allList: [],
      wishList: [],
      blackList: [],
      selectedItem: null,
      filter: "all",
      isOffline: false
    }
  },
  async mounted() {
    this.wishList = localStorage.getItem("my_eshop_wish")?.split(",") || [];
    this.blackList = localStorage.getItem("my_eshop_black")?.split(",") || [];

    try {
      const response = await axios.get("http://localhost:3000/api/games");
      this.allList = response.data;
    } catch (error) {
      console.error("Ошибка загрузки игр:", error);
      this.allList = select.response.docs;
    }
  },
  methods: {
    itemSelected(item) {
      this.selectedItem = item;
    },
    toBlackList() {
      this.blackList.push(this.selectedItem.fs_id);
      localStorage.setItem("my_eshop_black", this.blackList);
      this.selectedItem = null;
    },
    toWishList() {
      this.wishList.push(this.selectedItem.fs_id);
      localStorage.setItem("my_eshop_wish", this.wishList);
      this.selectedItem = null;
    },
    toAllList() {
      const indexBlack = this.blackList.indexOf(this.selectedItem.fs_id);
      if (indexBlack >= 0) {
        this.blackList.splice(indexBlack, 1);
      }
      const indexWish = this.wishList.indexOf(this.selectedItem.fs_id);
      if (indexWish >= 0) {
        this.wishList.splice(indexWish, 1);
      }
      localStorage.setItem("my_eshop_black", this.blackList);
      localStorage.setItem("my_eshop_wish", this.wishList);
      this.selectedItem = null;
    }
  },
  computed: {
    filteredList() {
      if (this.filter === "all") {
        return this.allList.filter((item) => {
          return !this.wishList.includes(item.fs_id) && !this.blackList.includes(item.fs_id);
        });
      }
      if (this.filter === "wish") {
        return this.allList.filter((item) => {
          return this.wishList.includes(item.fs_id);
        });
      }
      if (this.filter === "black") {
        return this.allList.filter((item) => {
          return this.blackList.includes(item.fs_id);
        });
      }
      return [];
    }
  },
  components: {
    GameViewer,
    GameItemsList
  }
}
</script>

<style>
html, body {
  height: 100%;
  padding: 0;
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #d0d0d0;
  background-color: black;
  margin: 0;
  padding: 0;
  height: 100vh;
}

.wrapper {
  display: grid;
  grid-template-columns: auto 240px;
  position: relative;
  height: calc(100% - 2em);
  overflow: hidden;
}

.top-panel {
  margin: 0 0.4em;
  height: 2em;
  display: flex;
  justify-content: left;
  align-items: center;
  gap: 0.3em;
}

select {
  padding: 0.1em;
  color: yellow;
  background-color: #222222;
}

.offline {
  background-color: #e60012;
  height: 1em;
  border-radius: 1em;
  padding: 0.2em 1em;
  color: white;
  font-size: 0.7em;
  margin-right: 2em;
}
</style>
