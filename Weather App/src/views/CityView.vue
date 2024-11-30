<template>
    <div>
        <Suspense>
            <AsyncCityView />
            <template #fallback>
                <p class="text-white text-xl text-center">Loading...</p>
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import AsyncCityView from './AsyncCityView.vue';


</script>
