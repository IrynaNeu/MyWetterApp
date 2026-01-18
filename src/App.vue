<script setup>
import { ref, watch, computed } from "vue";
import axios from "axios"

import defaultPic from "@/assets/background_pic/rainbow.jpg";
import clear_sky from "@/assets/background_pic/clear_sky.jpg";
import few_clouds from "@/assets/background_pic/few_clouds.jpg";

import scattered_clouds from "@/assets/background_pic/scattered_clouds.jpg";
import broken_clouds from "@/assets/background_pic/broken_clouds.jpg";

import overcast_clouds from "@/assets/background_pic/overcast_clouds.jpg";
import mist_fog from "@/assets/background_pic/fog.jpg";
import haze from "@/assets/background_pic/haze.jpg";

import smoke_dust from "@/assets/background_pic/smoke.jpg";
import sand from "@/assets/background_pic/sandsturm.jpg";

import drizzle_light_rain from "@/assets/background_pic/light_rain.jpg";
import moderate_rain from "@/assets/background_pic/moderate_rain.jpg";

import heavy_rain from "@/assets/background_pic/heavy_rain.jpg";
import thunderstorm from "@/assets/background_pic/thunderstorm.jpg";

import snow_light_snow from "@/assets/background_pic/light_snow.jpg";
import heavy_snow from "@/assets/background_pic/heavy_snow.jpg";

import sleet from "@/assets/background_pic/sleet.jpg";

import night_sky from "@/assets/background_pic/stars_sky.jpg";
import evening_time from "@/assets/background_pic/sunset.jpg";


const city = ref("");
const error = ref("");
const description = ref("")
const temp = ref("");
const feelsLike = ref("");
const minTemp = ref("")
const maxTemp = ref("")

const wetterBilder = {
  "Klarer Himmel": clear_sky,
  "Ein paar Wolken": few_clouds,
  "Mäßig bewölkt": scattered_clouds,
  "Überwiegend bewölkt": broken_clouds,
  "Bedeckt": overcast_clouds,
  "Trüb": mist_fog,
  "Nebel": mist_fog,
  "Lufttrübung": haze,
  "Rauch": smoke_dust,
  "Staub": smoke_dust,
  "Sand": sand,
  "Leichter Nieselregen": drizzle_light_rain,
  "Leichter Regen": drizzle_light_rain,
  "Mäßiger Regen": moderate_rain,
  "Starker Regen": heavy_rain,
  "Gewitter": thunderstorm,
  "Schnee": snow_light_snow,
  "Mäßiger Schnee": snow_light_snow,
  "Starker Schneefall": heavy_snow,
  "Schneeregen": sleet,
};

const weatherImage = computed(() => {
  const h = new Date().getHours();
  const hasCity = city.value.trim();

  if (!hasCity) {
    if (h >= 18 && h < 22) return evening_time;
    if (h >= 22 || h < 5) return night_sky;
    return defaultPic;
  }

  if (h >= 18 && h < 22) return evening_time;
  if (h >= 22 || h < 5) return night_sky;

  return wetterBilder[description.value] || defaultPic;
});
// Watch: reagiert auf city 
watch(city, (newCity) => {
  if (!newCity.trim()) description.value = "";
});

async function zeigeWetter() {
  error.value = "";
  if (city.value.trim().length < 3) {
    error.value = "Die Eingabe ist nicht vollständig!";
    return;
  }
  try {
    const res = await axios.get(

      `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=metric&appid=b8a52a698f64d295fd52fc4775488389&lang=de`
    );
    description.value = res.data.weather[0].description;
    temp.value = "Temperatur: " + Math.round(res.data.main.temp) + " °C";
    feelsLike.value = "Fühlt sich wie: " + Math.round(res.data.main.feels_like) + " °C";
    minTemp.value = "Min Temperatur: " + Math.round(res.data.main.temp_min) + " °C";
    maxTemp.value = "Max Temperatur: " + Math.round(res.data.main.temp_max) + " °C";


  } catch (err) {
    const status = err?.response?.status;

    if (status === 404) {
      error.value = "Stadt nicht gefunden. Bitte Schreibweise prüfen!"
    }
    else if (status === 401) {
      error.value = "API-Key ungültig/gesperrt (401).";
    }
    else {
      error.value = "API-Fehler!";
    }
    //alte Daten zurücksetzen
    description.value = "";
    temp.value = "";
    feelsLike.value = "";
    maxTemp.value = "";
    minTemp.value = "";
  }

};

</script>

<template>
  <div class="background_container">
    <transition-group name="background_change" tag="div" class="background_stack">
      <div v-for="(img) in [weatherImage]" :key="img" class="background_image"
        :style="{ backgroundImage: `url('${img}')` }"></div>
    </transition-group>

    <div class="main_container">
      <header>
        <h3>Wetter App</h3>
      </header>

      <main>
        <div class="weatherCity">
          <h1>Wetter in <span class="yourCity ">
              {{ city == "" ? "Ihren Stadt" : city }}</span> anzeigen</h1>
          <input v-model="city" type="text" placeholder="Geben Sie die Stadt ein" class="input_city"
            @keyup.enter="zeigeWetter()">
          <p v-if="error" class="error_message">{{ error }}</p>
          <button @click="zeigeWetter()">Wetter zeigen</button>

        </div>

        <transition name="weather_info_change">
          <div class="weather_info" v-show="description !== '' && city.trim() && !error">
            <strong>
              <p> {{ description }} <br> {{ temp }} <br> {{ feelsLike }} <br>
                {{ minTemp }} <br>{{ maxTemp }}</p>
            </strong>
          </div>
        </transition>
      </main>
    </div>
  </div>
</template>

<style scoped>
.background_container {
  position: relative;
  width: 100%;
  min-height: 100vh;
  overflow: hidden;
}

.background_stack {
  position: absolute;
  inset: 0;
}

.main_container {
  position: relative;
  z-index: 10;
  width: 100%;
  min-height: 100vh;
  overflow: hidden;
}

.background_image {
  position: absolute;
  inset: 0;
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  opacity: 1;
  transform: scale(1);
  filter: blur(0);
}

header {
  width: 100%;
  display: flex;
  justify-content: center;
  justify-content: center;
  margin-top: 2em;

}

header h3 {
  background: linear-gradient(315deg,
      rgba(109, 78, 186, 0.514),
      rgba(61, 146, 88, 0.584),
      rgba(31, 108, 150, 0.514));
  color: rgb(239, 241, 244);
  box-shadow: 5px 10px 15px 2px rgba(0, 0, 0, 0.3);
  border-radius: 8px;
  padding: 6px 20px;
  margin-top: 0.5em;
  font-size: clamp(1.2em, 2vw, 2.5em);
}

main {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-top: 3em;
}

.weatherCity {
  width: 90%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  /* gap: 10px; */
}

.input_city {
  width: min(80%, 700px);
  padding: 0.8em 0.6em;
  font-size: 20px;
  text-align: center;
  margin-bottom: 1.6em;
  font-size: clamp(1.4em, 1.5vw, 1.7em);
}

.input_city::placeholder {
  text-align: center;
  font-size: clamp(1em, 1.5vw, 1.7em);
}

span {
  margin: 0 10px;
}

h1 {
  width: min(90%, 900px);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  text-align: center;
  background: linear-gradient(315deg,
      rgba(109, 78, 186, 0.514),
      rgba(61, 146, 88, 0.584),
      rgba(31, 108, 150, 0.514));
  color: rgb(239, 241, 244);
  box-shadow: 5px 10px 15px 2px rgba(0, 0, 0, 0.3);
  border-radius: 8px;
  padding: 12px 16px;
  font-size: 1.3em;
  display: flex;
  justify-content: center;
}

.yourCity {
  color: rgb(209, 227, 143);
}

button {
  width: min(50%, 200px);
  padding: 10px;
  box-shadow: 1px 1px 4px black;
  background: linear-gradient(315deg,
      rgba(219, 155, 219, 0.403),
      rgb(230, 238, 232),
      rgb(115, 188, 237));
  border: none;
  border-radius: 6px;
  color: rgb(37, 34, 34);
  font-size: clamp(1em, 2vw, 1.4em);
}

.weather_info {
  width: min(600px, 70%);
  height: auto;
  margin-top: 0.9em;
  margin-bottom: 1em;
  background-color: brown;
  background: linear-gradient(315deg,
      rgba(9, 99, 65, 0.663),
      rgba(230, 238, 232, 0.678),
      rgba(115, 188, 237, 0.688));
  padding: 10px;
  font-size: clamp(1.5em, 2vw, 2.2em);
  text-align: center;
  border-radius: 15px;
}

.weather_info p {
  margin: 0;
  line-height: 1.5;
  font-weight: 700;
}

.background_change-enter-active,
.background_change-leave-active {
  transition:
    opacity 2s ease-in-out,
    transform 3s ease-out,
    filter 1.7s ease-in-out;
}

.background_change-enter-from {
  opacity: 0;
  transform: scale(1.05);
  filter: blur(3px)
}

.background_fade-leave-to {
  opacity: 0;
}

.error_message {
  width: min(70%, 700px);
  margin-top: 5px;
  text-align: center;

  color: rgb(18, 17, 17);
  font-weight: 700;
  font-size: clamp(1.5em, 2vw, 2.1em);

  background-color: rgba(225, 109, 91, 0.956);
  padding: 10px;
  border-radius: 10px;
}

@media (min-width: 481px) and (max-width: 768px) {

  h3 {
    margin-top: 1.5em;
  }

  h1 {
    font-size: 1.6em;
  }

  main {
    margin-top: 0.3em;
  }


  button {
    margin-bottom: 20px;
  }
}

@media (min-width: 769px) {

  header h3 {
    margin-bottom: 3em;
  }

  h1 {
    font-size: 2.1em;
  }

  .weatherCity {
    margin-top: 4em;
  }
}
</style>
