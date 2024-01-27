<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)"></CityCard>
    </div>
</template>

<script setup>

import axios from 'axios';
import { ref } from 'vue';
import CityCard from './CityCard.vue';
import { useRouter } from 'vue-router';

const savedCities = ref([]);
const getCities = async () => {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
    }

    const requests = [];
    savedCities.value.forEach( (city) => {
        requests.push(axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lon}&appid=6fdc1ef5856a1d664fd52ec323fa6f08&units=metric`));
    });

    const weatherData = await Promise.all(requests);

    // waiting
    await new Promise( (res) => {setTimeout(res, 500)});

    weatherData.forEach( (value, index) => {
        savedCities.value[index].weather = value.data;
    })
}

await getCities();

const router = useRouter();

const goToCityView = (city) => {
    router.push({
        name: 'cityView',
        params: { state: city.state, city: city.city },
        query: { id: city.id, lat: city.coords.lat, lon: city.coords.lon },
    });
};
</script>