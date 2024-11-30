<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- Banner -->
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>You are currently previewing this city, click the icon to start tracking this city.</p>
        </div>
        <!-- Weather overview -->
        <div class="flex flex-col items-center text-white py-4">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <p class="mb-2 text-sm">
                {{
                    new Date(weatherData.currentTime).toLocaleDateString("en-us", {
                        weekday: "short",
                        day: "2-digit",
                        month: "long",
                    })
                }} |
                {{
                    new Date(weatherData.currentTime).toLocaleTimeString("en-us", {
                        timeStyle: "short",
                    })
                }}
            </p>
            <p class="text-6xl mb-4">
                {{ Math.round(weatherData.main.temp) }}&deg;
            </p>
            <div class="text-center py-0">
                <p>
                    Feels Like {{ Math.round(weatherData.main.feels_like) }}&deg;
                </p>
                <p class="capitalize">
                    {{ weatherData.weather[0].description }}
                </p>
                <img class="w-[150px] h-auto m-0"
                    :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`">
            </div>
        </div>

        <hr class="border-white border-opacity-10 border w-full" />
        <!-- Hourly Weather -->
        <div class="max-w-screen-md w-full py-4">
            <div class="mx-8 text-white">
                <h2 class="mb-4 text-xl"> Hourly Weather</h2>
                <div class="flex gap-10 overflow-x-auto space-x-1 mb-2 hidden-scrollbar">
                    <div v-for="hourly in weatherDataHourly" :key="hourly.dt" class="flex flex-col gap-4 items-center">
                        <p class="whitespace-nowrap text-md ">
                            {{
                                new Date(hourly.currentTime).toLocaleDateString(
                                    "en-us",
                                    {
                                        hour: "numeric"
                                    }
                                )
                            }}
                        </p>
                        <img :src="`http://openweathermap.org/img/wn/${hourly.weather[0].icon}@2x.png`" />
                        <p class="text-white text-xl">{{ Math.round(hourly.main.temp) }}&deg;</p>
                        <p class="capitalize">{{ hourly.weather[0].description }}</p>
                    </div>
                </div>
            </div>
        </div>

        <hr class="border-white border-opacity-10 border w-full" />

        <div class="max-w-screen-md w-full py-4">
            <div class="mx-8 text-white">
                <p class="text-2xl">
                    Weather Today in {{ route.params.city }}, {{ route.params.state }}, {{ route.params.country }}
                </p>
                <div class="flex flex-row gap-10 py-4 w-full">
                    <div class="flex justify-start basis-1/2 w-full text-center">
                        <div class="text-white text-center mx-4">
                            <p class="text-xl pb-3"><i class="fa-solid fa-sun"></i> Day</p>
                            <p>{{ Math.round(weatherData.main.temp_max) }}&deg;</p>
                        </div>
                        <div class="text-white text-center ">
                            <p class="text-xl pb-3"><i class="fa-solid fa-moon"></i> Night</p>
                            <p>{{ Math.round(weatherData.main.temp_min) }}&deg;</p>
                        </div>
                    </div>
                    <div class="flex justify-end basis-1/2 w-full text-center">
                        <div class="text-white text-center mx-4">
                            <p class="text-xl pb-3"><i class="fa-solid fa-arrow-up-long"></i> Sunrise</p>
                            <p>

                                {{

                                    new Date(sunriseDate(weatherData)).toLocaleTimeString("en-us", {
                                        timeStyle: "short",
                                    })

                                }}

                            </p>
                        </div>
                        <div class="text-white text-center">
                            <p class="text-xl pb-3"><i class="fa-solid fa-arrow-down-long"></i> Sunset</p>
                            <p>
                                {{
                                    new Date(sunsetDate(weatherData)).toLocaleTimeString("en-us", {
                                        timeStyle: "short",
                                    })
                                }}
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="max-w-screen-md w-full pb-4">
            <div class="flex flex-row gap-10 p-0 w-full">
                <div class="justify-start basis-1/2 w-full text-white">
                    <div class="flex justify-start w-full text-center pb-3">
                        <p class="basis-1/2"><i class="fa-solid fa-droplet"></i> Humidity</p>
                        <p class="basis-1/2">{{ weatherData.main.humidity }}%</p>
                    </div>
                    <div class="flex justify-start w-full text-center pb-3">
                        <p class="basis-1/2"><i class="fa-solid fa-up-down"></i> Pressure</p>
                        <p class="basis-1/2">{{ weatherData.main.pressure }} hPa</p>
                    </div>
                    <div class="flex justify-start w-full text-center pb-3">
                        <p class="basis-1/2"><i class="fa-solid fa-eye"></i> Visibility</p>
                        <p class="basis-1/2">{{ weatherData.visibility }} m</p>
                    </div>
                    <div class="flex justify-start w-full text-center pb-3">
                        <p class="basis-1/2"><i class="fa-solid fa-cloud"></i> Cloud</p>
                        <p class="basis-1/2">{{ weatherData.clouds.all }}%</p>
                    </div>
                </div>
                <div class="justify-end basis-1/2 w-full text-white">
                    <div class="flex justify-start w-full text-center pb-3">
                        <p class="basis-1/2"><i class="fa-solid fa-wind"></i> Speed</p>
                        <p class="basis-1/2">{{ weatherData.wind.speed }} m/s</p>
                    </div>
                    <div class="flex justify-start w-full text-center pb-3">
                        <p class="basis-1/2"><i class="fa-solid fa-wind"></i> Direction</p>
                        <p class="basis-1/2">{{ weatherData.wind.deg }} m/d</p>
                    </div>
                    <div class="flex justify-start w-full text-center pb-3">
                        <p class="basis-1/2"><i class="fa-solid fa-water"></i> Sea</p>
                        <p class="basis-1/2">{{ weatherData.main.sea_level }} 	hPa</p>
                    </div>
                    <div class="flex justify-start w-full text-center pb-3">
                        <p class="basis-1/2"><i class="fa-solid fa-water"></i> Ground</p>
                        <p class="basis-1/2">{{ weatherData.main.grnd_level }} 	hPa</p>
                    </div>
                </div>
            </div>
        </div>

        <hr class="border-white border-opacity-10 border w-full" />

    </div>
</template>

<script setup>
// import { data } from 'autoprefixer';
import axios from 'axios';
import { useRoute } from 'vue-router';

let route = useRoute();
const ApiKey = import.meta.env.VITE_API_KEY_OPENWEATHERMAP;

// Get Current weather from openweathermap.org
let getWeatherData = async () => {

    try {

        let weatherData = await axios.post(`https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lon}&appid=${ApiKey}&units=metric`);
        // let weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lon}&exclude={part}&appid=${ApiKey}&units=imperial`);

        // cal current date & time
        let localOffset = new Date().getTimezoneOffset() * 60000;
        let utc = weatherData.data.dt * 1000 + localOffset;
        weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone;

        return weatherData.data;

    } catch (error) {
        console.log(error);
    }
}

// get Hourly weather from openweathermap.org
let getWeatherDataHourly = async () => {

    try {

        let weatherDataHourly = await axios.post(`https://api.openweathermap.org/data/2.5/forecast?lat=${route.query.lat}&lon=${route.query.lon}&appid=${ApiKey}&units=metric`);

        // cal hourly weather offset 
        let localOffset = new Date().getTimezoneOffset() * 60000;
        weatherDataHourly.data.list.forEach((hour) => {
            let utc = hour.dt * 1000 + localOffset;
            hour.currentTime = utc + 1000 * weatherDataHourly.data.city.timezone;
        });

        return weatherDataHourly.data.list;

    } catch (error) {
        console.log(error);
    }
}

let weatherData = await getWeatherData();
let weatherDataHourly = await getWeatherDataHourly();

let sunriseDate = (weatherObject) => {

    let localOffset = new Date().getTimezoneOffset() * 60000;
    let utc = weatherObject.sys.sunrise * 1000 + localOffset;
    let sunriseDateTime = utc + 1000 * weatherObject.timezone;

    return sunriseDateTime;
}

let sunsetDate = (weatherObject) => {

    let localOffset = new Date().getTimezoneOffset() * 60000;
    let utc = weatherData.sys.sunset * 1000 + localOffset;
    let sunsetDateTime = utc + 1000 * weatherObject.timezone;

    return sunsetDateTime;
}

// console.log(weatherData);

</script>

<style scoped>
/* Hide scrollbar but allow scrolling */
.hidden-scrollbar {
    scrollbar-width: none;
    /* Firefox */
    -ms-overflow-style: none;
    /* IE 10+ */

    /* Webkit browsers (Chrome, Safari) */
    &::-webkit-scrollbar {
        display: none;
    }
}
</style>
