<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Welcome to homework Vue.js style!",
      yoda: "Do or do not; there is no try!",
      places: [],
      newPlaceParams: {},
      currentPlace: {},
      editPlaceParams: {},
      showErrorMessage: false,
      errorMessage: "",
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("places.json").then((response) => {
        this.places = response.data;
        console.log("All Places", this.places);
      });
    },
    createPlace: function () {
      axios
        .post("places.json", this.newPlaceParams)
        .then((response) => {
          console.log("Place Created!", response.data);
          this.places.push(response.data);
          this.$refs.anyName.reset();
        })
        .catch((error) => console.log(error.response));
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      this.editPlaceParams = place;
      document.querySelector("#place-description").showModal();
    },
    updatePlace: function (place) {
      axios
        .patch("places/" + place.id, this.editPlaceParams)
        .then((response) => {
          console.log("Place Updated!", response.data);
        })
        .catch((error) => (this.errorMessage = error))
        .then((this.showErrorMessage = true));
    },
    destroyPlace: function (place) {
      axios
        .delete("places/" + place.id)
        .then((response) => {
          console.log("Place Destroyed!", response.data);
          var index = this.places.indexOf(place);
          this.places.splice(index, 1);
        })
        .catch((error) => (this.errorMessage = error))
        .then((this.showErrorMessage = true));
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h3>{{ yoda }}</h3>
    <div>
      Name:
      <input type="text" v-model="newPlaceParams.name" />
      Address:
      <input type="text" v-model="newPlaceParams.address" />
      <button v-on:click="createPlace()">Create</button>
    </div>
    <div v-for="place in places" v-bind:key="place.id">
      <h4>{{ place.name }}</h4>
      <p>{{ place.address }}</p>
      <button v-on:click="showPlace(place)">more info!</button>
    </div>

    <dialog id="place-description">
      <form method="dialog">
        <h1>Edit Place</h1>
        <p>
          Place Name:
          <input type="text" v-model="editPlaceParams.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="editPlaceParams.address" />
        </p>
        <button>Close</button>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>
