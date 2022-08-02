<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Welcome to a hacked Vue.js!",
      yoda: "Do or do not; there is no try!",
      count: 0,
      places: [],
      newPlaceParams: {},
      currentPlace: {},
      editPlaceParams: {},
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
      axios.patch("places/" + place.id, this.editPlaceParams).then((response) => {
        console.log("Place Updated!", response.data);
      });
    },
    destroyPlace: function (place) {
      axios.delete("places/" + place.id).then((response) => {
        console.log("Place Destroyed!", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
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
      Description:
      <input type="text" v-model="newPlaceParams.description" />
      Price:
      <input type="text" v-model="newPlaceParams.price" />
      Image_url:
      <input type="text" v-model="newPlaceParams.image_url" />
      <button v-on:click="createPlace()">Create</button>
    </div>
    <div v-for="place in places" v-bind:key="place.id">
      <h4>{{ place.name }}:</h4>
      <p>${{ place.price }}</p>
      <p><img :src="place.image_url" /></p>
      <button v-on:click="showPlace(place)">more info!</button>
    </div>

    <dialog id="place-description">
      <form method="dialog">
        <!-- <h1>{{ currentPlace.name }} - information:</h1>
        <p>Description: {{ currentPlace.description }}</p>
        <p>Price: ${{ currentPlace.price }}</p> -->

        <h1>Edit Place</h1>
        <p>
          Place Name:
          <input type="text" v-model="editPlaceParams.name" />
        </p>
        <p>
          Price:
          <input type="text" v-model="editPlaceParams.price" />
        </p>
        <p>
          Description:
          <input type="text" v-model="editPlaceParams.description" />
        </p>
        <p>
          Image URL:
          <input type="text" v-model="editPlaceParams.image_url" />
        </p>
        <button>Close</button>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
      </form>
    </dialog>

    <button @click="count++">Add 1</button>
    <h2>
      Count is:
      <count>{{ count }}!</count>
    </h2>
  </div>
</template>

<style>
count {
  color: green;
  font-style: italic;
}
</style>
