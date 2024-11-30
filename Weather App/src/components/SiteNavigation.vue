<template>
    <header class="sticky top-0 bg-weather-primary shadow-lg z-40">
        <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-2">

            <RouterLink :to="{ name: 'home' }">
                <dir class="flex items-center gap-3 flex-1">
                    <i class="fa-solid fa-sun text-2xl"></i>
                    <p class="text-2xl">The Local Weather</p>
                </dir>
            </RouterLink>
            <div class="flex gap-3 flex-1 justify-end">
                <i class="fa-solid fa-circle-info text-xl hover:text-weather-primary duration-150 cursor-pointer"
                    @click="toggleModal"></i>
                <span v-if="!checkCity">
                    <i class="fa-solid fa-plus text-xl hover:text-weather-primary duration-150 cursor-pointer"
                        @click="addCityToFavourite()" v-if="route.query.preview"></i>
                </span>
            </div>

            <BaseModal :modalActive="modalActive" @closeModal="toggleModal">
                <div class="text-black">
                    <h1 class="text-4xl mb-1">About:</h1>
                    <p class="mb-4">The Local Weather allows you to track the current and future waether of cities of
                        your choising</p>
                    <h2 class="text-2xl">How to work:</h2>
                    <ol class="list-decimal list-inside mb-4">
                        <li>
                            Search od your city by entering the name into the search bar
                        </li>
                        <li>
                            Select a city within the result, this will take yo to the current weather for your
                            selection.
                        </li>
                        <li>
                            Track the city by clicking on the '+' icon in the top eight. this will save the city to view
                            at a later time on the home page.
                        </li>
                    </ol>
                    <h2 class="text-2xl">Removing a city:</h2>
                    <p class="mb-4">if you no longer wish to track a city. simply select the city within the home page.
                        At the button of the page, there will be an option to remove the city </p>
                </div>
            </BaseModal>
        </nav>
    </header>
</template>

<script setup>
import { RouterLink, useRouter, useRoute } from 'vue-router';
import BaseModal from './BaseModal.vue';
import { computed, ref } from 'vue';
import { uid } from 'uid';
import Router from '@/router';


const modalActive = ref(null);
const route = useRoute();
const router = useRouter();

// this function work just for boolean type return reverse default value
const toggleModal = () => {
    modalActive.value = !modalActive.value;
};

// declare new array with ref 
// const cities = ref([]);

// function add cities in local storage 
// const addCity = () => {

//     if (localStorage.getItem('savedCities')) {
//         cities.value = JSON.parse(localStorage.getItem('savedCities'));
//     }

//     /*
//      *     Create new object with city data 
//      *     uid its function create unique identifier for object 
//      */
//     const locationObj = {
//         id: uid(),
//         city: route.params.city,
//         state: route.params.state,
//         country: route.params.country,
//         lat: route.query.lat,
//         lon: route.query.lon,

//     };

//     // add object to array cities 
//     cities.value.push(locationObj);
//     /*
//     *   The JSON.stringify() static method converts a JavaScript value to a JSON string
//     *   localStorage.setItem() accpet keyName, keyValue and KeyValue accepte just string 
//     */
//     localStorage.setItem("savedCities", JSON.stringify(cities.value));

//     let query = Object.assign({}, route.query);
//     delete query.preview;
//     router.replace({ query });
// };

const addCityToFavourite = () => {

    try {

        let FavouriteCitiesData = [];

        // get current data from localstorage if we have it 
        const favouriteCities = localStorage.getItem('favouriteCities');

        // test if localstorage have data if true start operation if not add city direct 
        if (favouriteCities) {

            // parse data with json parse method 
            let parsedFavouriteCities = JSON.parse(favouriteCities);

            // FavouriteCitiesData.value = JSON.parse(favouriteCities);

            for (let i = 0; i < parsedFavouriteCities.length; i++) {
                // check if we are added this city
                if (parsedFavouriteCities[i].lat == route.query.lat && parsedFavouriteCities[i].lon == route.query.lon) {
                    return true;
                }
                FavouriteCitiesData.push(parsedFavouriteCities[i]);
            }

            FavouriteCitiesData.push(
                {
                    "city": route.params.city,
                    "state": route.params.state,
                    "country": route.params.country,
                    "lat": route.query.lat,
                    "lon": route.query.lon,
                }
            );
            // add item to localstorage after update 
            localStorage.setItem("favouriteCities", JSON.stringify(FavouriteCitiesData));

        }
        // if we don't have any data in local storage create new one and add new item with city data 
        else if (favouriteCities === null) {

            FavouriteCitiesData.push(
                {
                    "city": route.params.city,
                    "state": route.params.state,
                    "country": route.params.country,
                    "lat": route.query.lat,
                    "lon": route.query.lon,
                }
            );

            localStorage.setItem("favouriteCities", JSON.stringify(FavouriteCitiesData));
        }

    } catch (error) {
        console.log(error);
    }

    Router.push({ name: "home" });

}

/**
 * checkCity is a function 
 */
const checkCity = computed(() => {

    // get current data from localstorage if we have it 
    const favouriteCities = localStorage.getItem('favouriteCities');
    // parse data with json parse method 
    let parsedFavouriteCities = JSON.parse(favouriteCities);

    if (favouriteCities) {
        for (let i = 0; i < parsedFavouriteCities.length; i++) {
            // check if we are added this city
            if (parsedFavouriteCities[i].lat == route.query.lat && parsedFavouriteCities[i].lon == route.query.lon) {
                return true;
            }
        }
    }
});

// console.log(checkCity.value)


</script>
