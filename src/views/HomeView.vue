<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input @input="getSearchResults" v-model="searchQuery" type="text" placeholder="Search for a city or state" class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">
      <ul class="absolute bh-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="weatherapiSearchResults || locationSearchResults">
        
        <p class="py-2" v-if="searchError">Sorry, something went wrong, please try again.</p>

        <!-- <p class="py-2" v-if="!searchError && weatherapiSearchResults.length === 0 && locationSearchResults.length === 0">No results match your query, try a different term.</p> -->
        
        <template v-else=" weatherapiSearchResults.length !==0 && locationSearchResults.length !==0 ">
          <div class="py-2 cursor-pointer" @click="previewCity(weatherapiSearchResults, locationSearchResults)">
            <h3>{{ locationSearchResults.name }}, {{ locationSearchResults.region }}, {{ locationSearchResults.country }}</h3>
            
            <!-- <h3>Localtime: {{ locationSearchResults.localtime }}</h3>
            <h3>Temp in Celsius: {{ weatherapiSearchResults.temp_c }}</h3>
            <h3>Feels like in Celsius: {{ weatherapiSearchResults.feelslike_c }}</h3>
            <h3>Wind in kph: {{ weatherapiSearchResults.wind_kph }}</h3> -->
            
          </div>
        </template>

      </ul>
    </div>

    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList></CityList>
        <template #fallback>
          <CityCardSkeleton></CityCardSkeleton>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from '../components/CityList.vue';
import CityCardSkeleton from '../components/CityCardSkeleton.vue';

const weatherapiAPIKey = "de9b9e3479204b17bd502843242601";

const searchQuery = ref("");
const queryTimeout = ref(null);
const weatherapiSearchResults = ref(null);
const locationSearchResults = ref(null);
const searchError = ref(null);

const router = useRouter();
const previewCity = (weatherapiSearchResults, locationSearchResults) => {
  const city = locationSearchResults.name;
  const state = locationSearchResults.region;
  // console.log(city, state);
  router.push({
    name: 'cityView',
    params: {state: state, city: city},
    query: {
      lat: locationSearchResults.lat,
      lon: locationSearchResults.lon,
      preview: true, 
    }
  })
}

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout( async ()=>{
    if(searchQuery.value !== ""){
      try{
        const result = await axios.get(
          `https://api.weatherapi.com/v1/current.json?key=${ weatherapiAPIKey }&q=${searchQuery.value}}&alert=yes`
          // `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );
        weatherapiSearchResults.value = result.data.current;
        locationSearchResults.value = result.data.location;
      }
      catch (e) {
        console.log(e.response.request.status);
        searchError = true;
      }
      return;
    }
    weatherapiSearchResults.value = null;
    locationSearchResults.value = null;
  }, 300);
};

</script>
