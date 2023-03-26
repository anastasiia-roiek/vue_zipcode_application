<template>
  <div class="container">
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li class="breadcrumb-item"><a href="#">Home</a></li>
        <li class="breadcrumb-item active" aria-current="page">ZIP Code Search</li>
      </ol>
    </nav>

    <h1 class="mt-4 mb-4">ZIP Code Search</h1>
    <form @submit.prevent="search" class="form-inline mb-4">
      <label for="zip" class="mr-2">Enter ZIP Code:</label>
      <input 
        type="text" 
        id="zip" 
        v-model="zipCode" 
        class="form-control mr-2"
      >
      <button type="submit" class="btn btn-primary">Search</button>
      <button 
        v-if="close" 
        type="button" 
        @click="getIPInfo" 
        class="btn btn-primary ml-2"
      >
        IP Lookup
      </button>
      <button 
        v-if="close" 
        type="button" 
        @click="clearData" 
        class="btn btn-danger ml-2"
      >
        Clear Data
      </button>
    </form>

    <p v-if="loading">Loading...</p>
    <p v-if="error">Enter the data...</p>

    <div v-if="result" class="card">
      <div class="card-body">
        <h2 class="card-title">Results for {{ zipCode }}:</h2>
        <p class="card-text">State: {{ state }}</p>
        <p class="card-text">City: {{ city }}</p>
      </div>
    </div>

    <div v-if="ipInfo.ip" class="card">
      <div class="card-body">
        <h2 class="card-title">IP Info:</h2>
        <p class="card-text">IP Address: {{ ipInfo.ip }}</p>
        <p class="card-text">ISP: {{ ipInfo.isp }}</p>
        <p class="card-text">City: {{ ipInfo.city }}</p>
        <p class="card-text">Region: {{ ipInfo.region }}</p>
        <p class="card-text">Country: {{ ipInfo.country_name }}</p>
      </div>      
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      zipCode: '',
      state: '',
      city: '',
      ipInfo: {},
      result: false,
      close: false,
      loading: false,
      error: false,
    };
  },
  methods: {
    async search() {
      if (!this.zipCode) {
        this.error = true;
        return console.log(error);
      }
      this.error = false;
      this.ipInfo = {};
      this.loading = true;
      axios
        .get(`https://api.zippopotam.us/us/${this.zipCode}`)
        .then((response) => {
          const data = response.data;
          this.state = data.places[0].state;
          this.city = data.places[0]['place name'];
          this.result = true;
          this.close = true;
          this.loading = false;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async getIPInfo() {
      this.result = false;
      this.loading = true;
      axios
        .get('https://ipapi.co/json/')
        .then((response) => {
          this.ipInfo = response.data;
          this.loading = false;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    clearData() {
      this.zipCode = '';
      this.state = '';
      this.city = '';
      this.ipInfo = {};
      this.result = false;
      this.close = false;
      this.loading = false;
      this.error = false;
    },
  },
};
</script>
