<template>
    <div class="flex flex-col flex-1 items-center">
        <div class="flex items-center gap-2 py-2 text-white cursor-pointer duration-150 hover:text-green-500" @click="router.push({ name: 'home' });">
            <i class="fa-solid fa-home"></i>
            <p>Home</p>
        </div>
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>You are currently viewing this city, click the "+" icon to start tracking this city</p>
        </div>

        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <h1 class="text-2xl mb-2">{{ route.params.state }}</h1>
            <p class="text-sm mb-12">
                {{ 
                    new Date(weatherData.currentTime).toLocaleDateString("en-us",
                    {
                        weekday: "short",
                        day: "2-digit",
                        month: 'long',
                    }) 
                }}
                {{ 
                    new Date(weatherData.currentTime).toLocaleTimeString("en-us",
                    {
                        timeStyle: "short"
                    })
                }}
            </p>
            <p class="text-8xl mb-8">
                {{ Math.round(weatherData.main.temp) }}&deg;
            </p>
            <p>
                Feels like
                {{ Math.round(weatherData.main.feels_like) }}&deg;
            </p>
            <p class="capitalize">
                {{ weatherData.weather[0].description }}
            </p>
            <img class="w-[150px] h-auto"
                :src="
                `http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`
                "
                alt=""
            />
        </div>

        <hr class="border-white border-opacity-10 border w-full"/>

        <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500" @click="removeCity">
            <i class="fa-solid fa-trash"></i>
            <p>Remove City</p>
        </div>

        <hr class="border-white border-opacity-10 border w-full"/>

        <!-- <div class="max-w-screen w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-4">7 Day Forecast</h2>
                <div v-for="day in weatherData.daily" :key="day.dt" class="flex items-center">
                        <p class="flex-1">
                            {{ 
                            new Date(day.dt * 1000).toLocaleTimeString("en-us",
                                {
                                    weekday: "long",
                                }
                            )
                            }}
                        </p>
                        <img
                            class="w-[50px] h-[50px] object-cover"
                            :src="
                                `http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`
                            "
                            alt=""
                            />
                        <div class="flex gap-2 flex-1 justify-end">
                            <p>H: {{ Math.round(day.temp.max) }}</p>
                            <p>L: {{ Math.round(day.temp.min) }}</p>
                        </div>
                    </div>
            </div>
        </div> -->

    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';


// 6fdc1ef5856a1d664fd52ec323fa6f08
// e40f1f6655aa9677cf2797c24d10ba3c
const openweatherAPIKey = "6fdc1ef5856a1d664fd52ec323fa6f08";

const route = useRoute();
const getWeatherData = async () => {
    try {
        // const weatherData = await axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lon}&exclude={part}&appid=${openweatherAPIKey}&units=imperial`);
        const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lon}&appid=${openweatherAPIKey}&units=metric`);
                
        // computing current date and time
        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData.data.dt*1000 + localOffset;

        weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone;

        // waiting
        await new Promise( (res) => {setTimeout(res, 500)});

        return weatherData.data;

    } catch(err) {
        console.log(err.message);
    }
};

const router = useRouter();

const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem('savedCities'));
    
    const updatedCities = cities.filter( (city) => city.id !== route.query.id);
    
    localStorage.setItem('savedCities', JSON.stringify(updatedCities));

    router.push({
        name: 'home',
    });
};

const weatherData = await getWeatherData();
console.log(weatherData);

</script>