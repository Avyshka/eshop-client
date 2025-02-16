<template>
  <div id="app">
    <div class="top-panel">
      <div class="left">
        <div v-if="isOffline" class="offline">offline</div>
        <div class="input">
          <label for="start">Start from:</label>
          <input type="text" id="start" v-model="start" v-on:input="loadGames"/>
        </div>
        <select v-model="filter">
          <option value="all">All</option>
          <option value="wish">Wish</option>
          <option value="black">Black</option>
        </select>
      </div>
      <div class="right">
        <button v-if="selectedItem && filter === 'all'" v-on:click="toBlackList">To black list</button>
        <button v-if="selectedItem && filter === 'all'" v-on:click="toWishList">To wish list</button>
        <button v-if="selectedItem && filter !== 'all'" v-on:click="toAllList">Remove from list</button>
      </div>
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
      start: 0,
      filter: "all",
      isOffline: false
    }
  },
  async mounted() {
    this.wishList = localStorage.getItem("my_eshop_wish")?.split(",") || [];
    this.blackList = localStorage.getItem("my_eshop_black")?.split(",") || [];
    await this.loadGames();
  },
  methods: {
    async loadGames() {
      try {
        const response = await axios.get(`https://eshop-server-43a9.onrender.com/api/games?start=${this.start}`);
        this.allList = response.data;
      } catch (error) {
        this.isOffline = true;
        console.error("Error loading games:", error);
        this.allList = select.response.docs;
      }
    },
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
  grid-template-columns: auto 280px;
  position: relative;
  height: calc(100% - 2em);
  overflow: hidden;
}

.top-panel {
  margin: 0 0.4em;
  height: 2em;
  display: flex;
  justify-content: space-between;
  gap: 0.3em;
  align-items: center;
}

.left {
  display: flex;
  gap: 0.5em;
}

.right {
  display: flex;
  gap: 0.3em;
}

.input {
  margin: auto;
}

.input label {
  font-size: 0.8em;
  color: #666;
}

.input input {
  width: 50px;
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
  margin: auto;
}
</style>
