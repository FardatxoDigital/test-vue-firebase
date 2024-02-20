<template>
  <div class="addcard-wrapper">
    <div :class="{ 'addcard-button': true, hidden: showInputs }">
      <button v-on:click="showInputsDiv()">ADD NEW USER</button>
      <button v-on:click="resetUserList()">RESET USER LIST</button>
    </div>
    <div :class="{ 'addcard-inputs': true, hidden: !showInputs }">
      <input v-model="newUser.nombre" placeholder="Name" required />
      <input v-model="newUser.telefono" placeholder="Phone" required />
      <input v-model="newUser.direccion" placeholder="Address" required />
      <input v-model="newUser.pais" placeholder="City" required />
      <div class="addcard-input-buttons-wrapper">
        <button v-on:click="addNewCard">ADD USER!</button>
        <button v-on:click="cancelAddNewCard">X</button>
      </div>
    </div>
    <div :class="{'empty-modal-wrapper': true, hidden: !showModal}">
  <div class="empty-modal">
    <p>Please fill in all fields.</p>
    <button v-on:click="showEmptyFieldModal">CLOSE</button>
  </div>
  </div>
  </div>
  
</template>

<script>
import defaultUsers from "./defaultUsers";
import axios from "axios";

export default {
  props: {
    apiData: Object,
  },
  data() {
    return {
      defaultUsers: defaultUsers,
      showInputs: false,
      showModal: false,
      newUser: {
        id: -1,
        nombre: "",
        telefono: "",
        direccion: "",
        pais: "",
      },
    };
  },
  methods: {
    addNewCard() {
      if (!hasEmptyProperty(this.newUser)) {
        const lastId = this.apiData[this.apiData.length - 1].id;
        this.newUser.id = lastId + 1;
        this.apiData.push(this.newUser);
        this.updateDataBase(this.apiData);
        this.showInputs = false;
        this.newUser = {
          id: -1,
          nombre: "",
          telefono: "",
          direccion: "",
          pais: "",
        };
      } else {
        this.showEmptyFieldModal()
      }

      function hasEmptyProperty(object) {
        for (let prop in object) {
          if (object[prop] === "") {
            return true;
          }
        }
        return false;
      }
    },
    cancelAddNewCard() {
      this.showInputs = false;
    },
    showInputsDiv() {
      this.showInputs = true;
    },
    resetUserList() {
      const answer = confirm("This action will delete all users and load a default user list. Do you want to continue?")
      
      if (answer){
        this.updateDataBase(this.defaultUsers.users);
        this.$emit("updateApiData", this.defaultUsers.users);
      }
    },
    async updateDataBase(data) {
      try {
        const response = await axios.put(
          "https://practica-vue-34d51-default-rtdb.europe-west1.firebasedatabase.app/users.json",
          data
        );
      } catch (error) {
        console.error("Error al reemplazar la base de datos:", error);
      }
    },
    showEmptyFieldModal(){
        this.showModal = !this.showModal
    },
  },
};
</script>

<style scoped>
.hidden {
  display: none !important;
  visibility: hidden;
}

.empty-modal-wrapper{
    z-index: 10;
    position: absolute;
    width: 100vw;
    height: 100vh;
    top: 0;
    background-color: rgba(0,0,0,0.6);
    display:flex;
    justify-content: center;
}
.empty-modal{
    display: flex;
    flex-direction: column;
    justify-content: center;
    margin-top: 15%;
    width: 300px;
    height: 200px;
    background-color: #af8771;
    padding: 2em;
    border-radius: 15px;
    box-shadow: 6px 6px 10px #333;
    font-size: 120%;
    font-family: 'Courier New', Courier, monospace;
    color: #fff;
}

.empty-modal p {
    font-weight: bold;
}

.empty-modal button {
  font-family: "Courier New", Courier, monospace;
  font-weight: bolder;
  display: block;
  padding: 1em;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  border: 2px solid #82a197;
  background-color: #cff099;
}

.empty-modal button:hover {
    background-color: #82a197;
}


.addcard-wrapper {
  width: 100%;
  margin-bottom: 2em;
}

.addcard-button {
  width: fit-content;
  margin: auto;
  display: flex;
  gap: 2em;
  align-items: center;
}
.addcard-button button {
  margin: auto;
  font-family: "Courier New", Courier, monospace;
  font-weight: bolder;
  display: block;
  padding: 1em;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  border: 2px solid #82a197;
}

.addcard-button button:nth-child(1) {
  background-color: #cff099;
}

.addcard-button button:nth-child(1):hover {
  background-color: #95aa73;
  color: #fff;
}

.addcard-button button:nth-child(2) {
  background-color: #f0b799;
}

.addcard-button button:nth-child(2):hover {
  background-color: #af8771;
  color: #fff;
}

.addcard-inputs {
  width: 80%;
  max-width: 950px;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;
  gap: 1em;
  background-color: #7f69a3;
  padding: 1em 2em;
  border-radius: 8px;
  justify-content: center;
}

.addcard-inputs input {
  border: none;
  font-family: "Courier New", Courier, monospace;
  background-color: #e5e3d1;
  font-weight: bold;
  padding: 1%;
}

.addcard-input-buttons-wrapper {
  display: flex;
}
.addcard-input-buttons-wrapper button {
  font-family: "Courier New", Courier, monospace;
  font-weight: bolder;
  display: block;
  padding: 0.5em 1em;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.addcard-input-buttons-wrapper button:nth-child(1) {
  background-color: #cff099;
}

.addcard-input-buttons-wrapper button:nth-child(1):hover {
  background-color: #95aa73;
}

.addcard-input-buttons-wrapper button:nth-child(2) {
  background-color: #f0b799;
  margin-left: 10px;
  padding: 0 13px;
  font-family: cursive;
}

.addcard-input-buttons-wrapper button:nth-child(2):hover {
  background-color: #af8771;
}

@media only screen and (max-width: 520px) {
  .addcard-inputs {
    flex-direction: column;
    width: 72%;
    gap: 1em;
  }

  .addcard-inputs input {
    padding: 0.3em;
    font-size: 100%;
  }

  .addcard-input-buttons-wrapper button:nth-child(1) {
    width: 85%;
  }
}
</style>
