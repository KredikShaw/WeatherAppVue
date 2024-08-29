<template>
    <main class="weather-main">
        <section class="app-section">
            <section class="search-section">
                <main class="search-wrapper">
                    <input v-model="searchText" @keyup.enter="getWeatherData" type="text" class="search-input"
                    placeholder="Sofia">
                    <img @click="getWeatherData" class="search-button" src="/src/assets/icons/search.svg" alt="">
                </main>
            </section>
            
            <div v-show="!loading && !temperatureC && !temperatureF && !temperatureK" class="welcome-text">Start by entering a city name</div>

            <section v-if="loading" class="loading-section">
                <img src="/src/assets/icons/sunny.gif" alt="">
            </section>

            <section v-show="temperatureC || temperatureF || temperatureK" class="weather-section">
                <div class="welcome-text">Current temperature in <strong>{{lastCity()}}</strong></div>
                <main class="temperature-wrapper">
                    <div class="temperature-display">C°: {{ temperatureC }}</div>
                    <div class="temperature-display">F°: {{ temperatureF }}</div>
                    <div class="temperature-display">K°: {{ temperatureK }}</div>
                </main>
            </section>
        </section>
    </main>
</template>
<script>
import axios from 'axios';
import { toast } from 'vue3-toastify';

export default {
    data() {
        return {
            loading: false,
            searchText: null,
            temperatureC: null,
            temperatureF: null,
            temperatureK: null,
        }
    },
    mounted(){
        let lastCity = sessionStorage.getItem('last-city');

        if (lastCity) {
            this.searchText = lastCity;
            this.getWeatherData();
        }
    },
    methods: {
        lastCity(){
            return sessionStorage.getItem('last-city');
        },
        async getWeatherData() {
            this.temperatureC = null;
            this.temperatureF = null;
            this.temperatureK = null;

            if (!this.searchText) {
                toast.error("You must enter a city name first", {
                            autoClose: 3000,
                });

                return;
            }

            this.loading = true;

            axios.get(`https://localhost:7201/api/weather/get/${this.searchText}`)
                .then(response => {
                    if (response.data.success) {
                        this.temperatureC = response.data.temperatures.temperatureC;
                        this.temperatureF = response.data.temperatures.temperatureF;
                        this.temperatureK = response.data.temperatures.temperatureK;

                        sessionStorage.setItem('last-city', this.searchText);
                    }
                    else {
                        toast.error(response.data.errorMessage, {
                            autoClose: 3000,
                        });
                    }

                    this.loading = false;
                    this.searchText = null;
                });
        },
    },
}
</script>
