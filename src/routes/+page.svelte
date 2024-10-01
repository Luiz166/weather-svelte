<script lang="ts">
	import { Toggle } from 'flowbite-svelte';
	import { SearchOutline, MapPinAltOutline, SunOutline } from 'flowbite-svelte-icons';
	import { Card } from 'flowbite-svelte';
    import { Button, GradientButton } from 'flowbite-svelte';
	import axios from 'axios';

    let location = '';
    const api_key = import.meta.env.VITE_API_KEY;
    let isVisible:boolean = false;
    let weatherData:any = null;
    let forecast:any[] = [];

    function toggleVisible(){
        isVisible = !isVisible;
    }

    async function handleForm(){

        if(location != ''){
            await axios
            .post(`http://api.weatherapi.com/v1/current.json/forecast.json?key=${api_key}&q=${location}`)
            .then(res => {
                toggleVisible()
                weatherData = res;
                forecast = res.data.forecast.forecastday[0].hour;
            })
            .catch(err => {
                console.log(err)
            })
        console.log(forecast)        
        }else{
            alert('Insira algo')
        }
    }

    function getHour(time:string):string{
        return time.split(" ")[1].split(":")[0];
    }

</script>

<div class="w-screen h-screen flex flex-col items-center">
    <header class="flex justify-between w-1/2">
        <div>
            <h1 class="text-3xl font-bold">WeatherMe</h1>
        </div>
        <div class="flex space-x-10">
            <Toggle class="text-xl" color="purple">°F</Toggle>
            <button>
                <SunOutline size="lg"/>
            </button>
        </div>
    </header>
    <main class="w-full flex flex-col items-center mt-10 space-y-10">
        <form class="flex items-center justify-center w-1/2" on:submit={handleForm}>
            <div class="relative w-full">
                <input class="w-full px-3 rounded-l-lg peer placeholder-shown:px-7 duration-300 border-purple-500" bind:value={location} type="text" id="localeInput" placeholder="Procure um local..." />
                <label class="absolute left-1 top-2 peer-placeholder-shown:block hidden duration-300" for="localeInput">
                    <SearchOutline size="lg" />
                </label>
            </div>
            <GradientButton shadow color="purple" class="rounded-l-none rounded-r-lg h-full" type="submit">Pesquisar</GradientButton>
        </form>
        {#if weatherData}
        <Card padding="xl" size="lg" class=" duration-300 ease-in-out bg-gradient-to-br from-purple-500 to-purple-200 text-white">
            <h5 class="flex space-x-2">
                {weatherData.data.location.name}
                <MapPinAltOutline />
            </h5>
            <div class="flex items-center space-x-5 justify-center">
                <span class="text-center text-4xl mt-5"> {weatherData.data.current.temp_c}°C </span>
                <img class="w-10 h-10" src={weatherData.data.current.condition.icon} alt=""/>
            </div>
            <div class="flex flex-row w-full space-x-5 mt-5 justify-center">
                <div class="flex flex-col items-center">
                    <span>Humidade</span>
                    <span>{weatherData.data.current.humidity}%</span>
                </div>
                <div class="flex flex-col items-center">
                    <span>Visibilidade</span>
                    <span>{weatherData.data.current.vis_km}km</span>
                </div>
                <div class="flex flex-col items-center">
                    <span class="text-nowrap">Pressão do Ar</span>
                    <span>{weatherData.data.current.pressure_mb}hPa</span>
                </div>
                <div class="flex flex-col items-center">
                    <span>Vento</span>
                    <span>{weatherData.data.current.wind_kph}km/h</span>
                </div>
            </div>
        </Card>
        {/if}
        {#if forecast.length > 0}
            <h2 class="text-3xl font-semibold">Previsão de hoje</h2>
            <div class="flex items-center justify-center flex-row space-x-5 flex-wrap w-full">
                {#each forecast as hourData}
                <Card size="sm" class="duration-300 ease-in-out bg-gradient-to-br from-purple-500 to-purple-200 text-white items-center">
                    <span class="text-center font-bold">{getHour(hourData.time)}:00</span>
                    <img class="w-32 h-32" src={hourData.condition.icon} alt="" />
                    <span class="text-center text-xl">{hourData.temp_c}°C</span>
                </Card>
                {/each}
            </div>
        {/if}
    </main>
</div>
