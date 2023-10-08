<template>
    <div class="weather-card" :class="weatherConditionClass">
        <h1 class="app-title">Weather App</h1>
        <input v-model="city" placeholder="Enter city name" class="input-field">
        <button @click="getWeatherData">Get Weather</button>
        <transition name="fade">
            <div v-if="weatherData" class="weather-info">
                <h2 class="city-name">{{ weatherData.name }}</h2>
                <p class="temperature">Temperature: {{ weatherData.main.temp }}°C</p>
                <p class="weather-description">Weather: {{ weatherData.weather[0].description }}</p>
                <img class="weather-icon" :src="'https://openweathermap.org/img/wn/' + weatherData.weather[0].icon + '.png'" alt="Weather Icon" ref="weatherIcon">
            </div>
            <div v-else>
                <p class="loading-text">Loading...</p>
            </div>
        </transition>
    </div>
</template>

<script>
import axios from "axios";

export default {
    name: "WeatherCard",
    data() {
        return {
            weatherData: null,
            apiKey: "6357f322255f60897fa8639292783419",
            city: "", // Nilai awal input kosong
        };
    },
    computed: {
        weatherConditionClass() {
              if (this.weatherData && this.weatherData.weather && this.weatherData.weather.length > 0) {
                const weatherCode = this.weatherData.weather[0].id;
                if (weatherCode >= 200 && weatherCode < 600) {
                    // Cuaca hujan
                    return 'rainy';
                } else if (weatherCode >= 600 && weatherCode < 700) {
                    // Salju
                    return 'snowy';
                } else if (weatherCode >= 801 && weatherCode <= 804) {
                    // Mendung
                    return 'cloudy';
                } else {
                    // Cuaca cerah atau cuaca lainnya
                    return 'sunny';
                }
            } else {
                // Tidak ada data cuaca
                return '';
            }
        },
    },
    methods: {
        async getWeatherData() {
            try {
                const response = await axios.get(
                    `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${this.apiKey}&units=metric`
                );
                console.log("API Response:", response.data);
                this.weatherData = response.data;
                this.$nextTick(() => {
                    this.$refs.weatherIcon.classList.add('change');
                    setTimeout(() => {
                        this.$refs.weatherIcon.classList.remove('change');
                    }, 1000); // Hapus kelas `.change` setelah 1 detik
                });
            } catch (error) {
                console.error("Error fetching weather data:", error);
            }
        },
    },
};
</script>


<style scoped>
@keyframes changeBackgroundColor {
    0% {
        background-color: #f7b731; /* Start color for sunny weather */
    }
    25% {
        background-color: #4b7bec; /* Color change at 25% for cloudy weather */
    }
    50% {
        background-color: #10ac84; /* Color change at 50% for rainy weather */
    }
    100% {
        background-color: #f7b731; /* Back to start color for sunny weather */
    }
}

body {
    margin: 0;
    padding: 0;
    font-family: Arial, sans-serif;
    height: 100vh; /* Make sure the body covers the entire viewport height */
    display: flex;
    justify-content: center;
    align-items: center;
    animation: changeBackgroundColor 5s infinite; /* Apply the background color animation */
}

.weather-card {
    text-align: center;
    padding: 20px;
    border-radius: 10px;
    width: 300px;
    margin: 0 auto;
    transition: box-shadow 0.3s ease-in-out; /* Animasi perubahan bayangan */
}

.weather-card.sunny {
    background-color: #f7b731; /* Warna latar belakang untuk cuaca cerah */
    box-shadow: 0px 0px 20px 0px rgba(247, 183, 49, 0.7); /* Bayangan untuk cuaca cerah */
}

.weather-card.cloudy {
    background-color: #4b7bec; /* Warna latar belakang untuk cuaca mendung */
    box-shadow: 0px 0px 20px 0px rgba(75, 123, 236, 0.7); /* Bayangan untuk cuaca mendung */
}

.weather-card.rainy {
    background-color: #10ac84; /* Warna latar belakang untuk hujan */
    box-shadow: 0px 0px 20px 0px rgba(16, 172, 132, 0.7); /* Bayangan untuk hujan */
}

.weather-card {
    /* ... (kode CSS lainnya) ... */
}

/* ... (kode CSS lainnya) ... */
</style>
