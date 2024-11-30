<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input v-model="searchQuery" @input="getSearchResult" type="text" placeholder="Search For a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none">
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px] z-40"
        v-if="mapBoxsearchResult">
        <p v-if="searchError"> sorry, somthing went wrong, please try again. </p>
        <p v-if="!searchError && mapBoxsearchResult.length === 0"> No result match your query, try a different term.
        </p>
        <li v-for="searchResult in mapBoxsearchResult" :key="searchResult.id" @click="previewCity(searchResult)"
          class="py-2 cursor-pointer">
          {{ searchResult.properties.full_address }}
        </li>
      </ul>
    </div>
    <div class="pt-4 mb-8 relative">
      <CityCard/>
    </div>
  </main>
</template>


<script setup>
import axios from 'axios';
import { ref } from 'vue';
import router from '@/router';
import CityCard from '@/components/CityCard.vue';

const mapBoxAPIkey = import.meta.env.VITE_API_KEY_MAPBOXAPIKEY;

let mapBoxsearchResult = ref(null);

let searchQuery = ref('');

let queryTimeout = ref(null);

let searchError = ref(null);

let previewCity = (searchResult) => {

  let cityName = searchResult.properties.context.place.name;
  let countryName = searchResult.properties.context.country.name;
  let stateName = searchResult.properties.context.region.name;
  let latGeo = searchResult.properties.coordinates.latitude;
  let lonGeo = searchResult.properties.coordinates.longitude;

  router.push({
    name: "CityView",
    params: {country : countryName.replaceAll(" ", "-") , state: stateName.replaceAll(" ", "-"), city: cityName.replaceAll(" ", "-") },
    query: {
      lat: latGeo,
      lon: lonGeo,
      preview: true,
    }
  })

};

const getSearchResult = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const res = await axios.get(`https://api.mapbox.com/search/geocode/v6/forward?q=${searchQuery.value}&access_token=${mapBoxAPIkey}&type=place`);
        mapBoxsearchResult.value = res.data.features;
      } catch {
        searchError.value = true;
      }
      return;
    }
    mapBoxsearchResult.value = null;
  }, 300);

};
</script>