<script>
import axios from "axios";
import colors from "./colors.js";

export default {
  props: {
    apiData: Object,
  },
  data() {
    return {
      inputIsHidden: false,
      colors: colors.colors,
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      axios
        .get(
          "https://practica-vue-34d51-default-rtdb.europe-west1.firebasedatabase.app/users.json"
        )
        .then((response) => {
          this.$emit("updateApiData", response.data);
        })
        .catch((error) => {
          console.error("Error when obtain data: ", error);
        });
    },
    async updateDb(itemId) {
      try {
        const response = await axios.patch(
          `https://practica-vue-34d51-default-rtdb.europe-west1.firebasedatabase.app/users/${itemId}.json`,
          this.apiData[itemId]
        );

        console.log(response.data);
      } catch (error) {
        console.error("Error al actualizar la base de datos:", error);
      }
    },
    editCard(event) {
      const elementoPadre = event.target.parentElement.parentElement;

      elementoPadre.querySelector(".texts").classList.add("hidden");
      elementoPadre.querySelector(".inputs").classList.remove("hidden");
      elementoPadre.querySelector(".card-buttons-wrapper").classList.add("hidden");
      elementoPadre.querySelector(".save-button").classList.remove("hidden");
    },
    saveCard(event, itemId) {
      const elementoPadre = event.target.parentElement;

      elementoPadre.querySelector(".texts").classList.remove("hidden");
      elementoPadre.querySelector(".inputs").classList.add("hidden");
      elementoPadre.querySelector(".card-buttons-wrapper").classList.remove("hidden");
      elementoPadre.querySelector(".save-button").classList.add("hidden");

      console.log(itemId);
      this.updateDb(itemId);
    },
    generateImage(itemId) {
      const base = "https://robohash.org/user";
      return base + itemId;
    },
    generateColor(id) {
      const digit = id % 10;
      return this.colors[digit];
    },
    async removeCard(id) {
      const newArray = this.apiData.filter((item) => item.id != id);
      this.$emit("updateApiData", newArray);
      try {
        const response = await axios.put(
          "https://practica-vue-34d51-default-rtdb.europe-west1.firebasedatabase.app/users.json",
          newArray
        );
      } catch (error) {
        console.error("Error al reemplazar la base de datos:", error);
      }
    },
  },
};
</script>

<template>
  <ul class="user-cards-container">
    <li class="user-card" v-for="item in apiData" :key="item.id">
      <div
        class="top-color"
        :style="{ backgroundColor: generateColor(item.id) }"
      ></div>
      <div class="user-img-wrapper">
        <img class="user-img" :src="generateImage(item.id)" />
      </div>
      <div class="texts">
        <p class="user-name">{{ item.nombre }}</p>
        <p class="user-phone">{{ item.telefono }}</p>
        <p class="user-address">{{ item.direccion }}</p>
        <p class="user-country">{{ item.pais }}</p>
      </div>
      <div class="inputs hidden">
        <input id="input-name" v-model="item.nombre" />
        <input id="input-phone" v-model="item.telefono" />
        <input id="input-address" v-model="item.direccion" />
        <input id="input-country" v-model="item.pais" />
      </div>
      <div class="card-buttons-wrapper">
        <button v-on:click="editCard($event)">Update</button>
        <button v-on:click="removeCard(item.id)">Remove</button>
      </div>
      <button class="save-button hidden" v-on:click="saveCard($event, item.id)">
        Save data!
      </button>
    </li>
  </ul>
</template>

<style>
.user-cards-container {
  max-width: 1000px;
  margin: auto;
  display: flex;
  justify-content: center;
  gap: 20px;
  padding: 0;
  flex-wrap: wrap;
}
.user-card {
  background-color: #eee;
  list-style-type: none;
  padding: 1em;
  border-radius: 7px;
  width: 200px;
  color: #444;
  box-shadow: 5px 5px 6px #777;
  overflow: hidden;
}

.top-color {
  width: 116%;
  margin-left: -8%;
  margin-top: -1em;
  height: 100px;

  border-top-left-radius: 7px;
  border-top-right-radius: 7px;
}
.user-img-wrapper {
  width: 70px;
  height: 70px;
  border-radius: 50%;
  background-color: #72dbb8;
  outline: 2px solid #fff;
  position: relative;
  margin: auto;
  top: -30px;
  overflow: hidden;
}

.user-img {
  width: 100%;
  object-fit: cover;
}

.user-card p {
  text-align: center;
  font-family: "Courier New", Courier, monospace;
  margin-top: 0;
  margin-bottom: 0;
}

.user-name {
  margin-top: 0;
  margin-bottom: 1em !important;
  font-family: "Courier New", Courier, monospace;
  font-weight: bold;
}

.card-buttons-wrapper {
  display: flex;
}

.card-buttons-wrapper button:nth-child(1) {
  background-color: #e0f099;
}
.card-buttons-wrapper button:nth-child(2) {
  background-color: #f0bd99;
}

.save-button {
  background-color: #e0f099;
  width: 60% !important;
}

.user-cards-container button {
  width: 40%;
  margin: auto;
  display: block;
  margin-top: 1em;

  border: none;
  padding: 0.8em;
  border-radius: 7px;
  cursor: pointer;
  position: relative;
  bottom: 0;
}

.user-cards-container button:hover {
  background-color: #f1e3b4;
}

.user-cards-container input {
  width: 90%;
  margin: auto;
  display: block;
  margin-bottom: 0.4em;
  border: none;
  background-color: #e8dfc8;
  color: #111;
  font-family: "Courier New", Courier, monospace;
}

#input-name {
  margin-bottom: 1em !important;
}

.hidden {
  visibility: hidden;
  display: none !important;
}

@media only screen and (max-width: 520px) {
  .user-cards-container{
    gap:5px;
  }
  
  .user-card {
    width: 40%;
  }

  .top-color{
    width: 125%;
    margin-left: -9.5%;
  }
}
</style>
