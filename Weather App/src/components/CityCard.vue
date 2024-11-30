<template>
    <div v-if="citiesCheck">
        <div v-for="(cityData, index) in citiesData" :key="index"
            class="flex flex-row gap-10 py-4 w-full bg-weather-secondary p-4 my-3 transition duration-150 ease-out hover:ease-in">
            <div class="w-full flex flex-row"
                @click="weatherCityDetails(cityData.city, cityData.state, cityData.country, cityData.lat, cityData.lon)">
                <div class="city-state flex flex-col justify-start basis-1/2 w-full text-left px-3">
                    <p class="block text-2xl">{{ cityData.city }}</p>
                    <p class="block">{{ cityData.state }}</p>
                </div>
                <div class="temperature flex flex-col justify-end basis-1/2 w-full text-right px-3">
                    <p class="block text-2xl">{{ cityData.temp }}&deg;</p>
                    <p class="block">H {{ cityData.temp_max }}&deg; &ThickSpace; L {{ cityData.temp_min }}&deg;</p>
                </div>
            </div>
            <i class="fa-regular fa-circle-xmark bg-red-600 p-2 rounded text-3xl"
                v-on:click="deleteCityFromStorage(cityData.lat, cityData.lon)"></i>
        </div>
    </div>
</template>

<script setup>
import { computed, ref } from "vue";
import axios from "axios";
import router from "@/router";

const ApiKey = import.meta.env.VITE_API_KEY_OPENWEATHERMAP;

const citiesData = ref([]);
const citiesCheck = ref(false);

const getWeatherData = async (lat, lon) => {
    try {
        const response = await axios.post(
            `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${ApiKey}&units=metric`
        );
        return response.data;
    } catch (error) {
        console.error("Error fetching weather data:", error);
        return null;
    }
};

const loadCitiesWeather = async () => {
    const favouriteCities = localStorage.getItem("favouriteCities");

    if (favouriteCities) {
        const parsedFavouriteCities = JSON.parse(favouriteCities);

        const weatherPromises = parsedFavouriteCities.map(async (city) => {
            const weather = await getWeatherData(city.lat, city.lon);
            if (weather) {
                return {
                    temp: Math.round(weather.main.temp),
                    temp_min: Math.round(weather.main.temp_min),
                    temp_max: Math.round(weather.main.temp_max),
                    city: city.city.replaceAll("-", " "),
                    state: city.state.replaceAll("-", " "),
                    country: city.country.replaceAll("-", " "),
                    lat: city.lat,
                    lon: city.lon,
                };
            }
            return null;
        });

        // Wait for all promises to resolve
        const results = await Promise.all(weatherPromises);

        // Filter out null results and update reactive variables
        citiesData.value = results.filter((result) => result !== null);
        citiesCheck.value = true;
    } else {
        citiesCheck.value = false;
    }
};


const weatherCityDetails = (city, state, country, lat, lon) => {
    router.push({
        name: "CityView",
        params: { country: country.replaceAll(" ", "-"), state: state.replaceAll(" ", "-"), city: city.replaceAll(" ", "-") },
        query: {
            lat: lat,
            lon: lon,
        }
    })
}



const deleteCityFromStorage = (lat, lon) => {

    const favouriteCities = localStorage.getItem("favouriteCities");
    if (favouriteCities) {
        const citiesDataJson = JSON.parse(favouriteCities);

        const newCitiesData = [];

        try {
            for (let i = 0; i < citiesDataJson.length; i++) {
                if (citiesDataJson[i].lat !== lat && citiesDataJson[i].lon !== lon) {

                    newCitiesData.push(citiesDataJson[i]);

                }
            }
            localStorage.setItem("favouriteCities", JSON.stringify(newCitiesData));
            loadCitiesWeather();
        } catch (error) {
            console.log(error);
        }

    }

}

// Load cities' weather data when the component is mounted
loadCitiesWeather();
</script>

<style lang="scss" scoped></style>
