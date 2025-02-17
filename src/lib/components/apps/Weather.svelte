<script lang="ts">
  import { onMount } from 'svelte';
  import { slide } from 'svelte/transition';

  interface WeatherData {
    temp: number;
    feelsLike: number;
    humidity: number;
    windSpeed: number;
    condition: WeatherCondition;
    icon: string;
  }

  interface WeatherForecast {
    day: string;
    high: number;
    low: number;
    icon: string;
    precipitation: string;
  }

  interface HourlyForecast {
    time: string;
    temp: number;
    icon: string;
  }

  interface WeatherAlert {
    type: string;
    description: string;
    time: string;
  }

  type WeatherCondition = 'Clear' | 'Partly Cloudy' | 'Cloudy' | 'Rain' | 'Snow' | 'Thunderstorm' | 'Windy';

  let currentWeather: WeatherData = {
    temp: 72,
    feelsLike: 75,
    humidity: 65,
    windSpeed: 8,
    condition: 'Clear',
    icon: '☀️'
  };

  let forecast: WeatherForecast[] = [
    { day: 'Today', high: 75, low: 65, icon: '☀️', precipitation: '0%' },
    { day: 'Mon', high: 73, low: 63, icon: '🌤', precipitation: '20%' },
    { day: 'Tue', high: 68, low: 60, icon: '🌧', precipitation: '80%' },
    { day: 'Wed', high: 70, low: 62, icon: '⛅️', precipitation: '30%' },
    { day: 'Thu', high: 72, low: 64, icon: '☀️', precipitation: '0%' }
  ];

  let hourlyForecast: HourlyForecast[] = [
    { time: 'Now', temp: 72, icon: '☀️' },
    { time: '1PM', temp: 74, icon: '☀️' },
    { time: '2PM', temp: 75, icon: '🌤' },
    { time: '3PM', temp: 74, icon: '⛅️' },
    { time: '4PM', temp: 73, icon: '⛅️' },
    { time: '5PM', temp: 71, icon: '☀️' },
    { time: '6PM', temp: 69, icon: '☀️' },
    { time: '7PM', temp: 67, icon: '🌙' }
  ];

  let selectedLocation = 'Current Location';
  let locations = ['Current Location', 'New York', 'London', 'Tokyo', 'Paris'];
  let showLocationPicker = false;

  let weatherAlerts: WeatherAlert[] = [
    {
      type: 'Heat Advisory',
      description: 'High temperatures expected throughout the afternoon',
      time: '12:00 PM - 6:00 PM'
    }
  ];

  const weatherIcons: Record<WeatherCondition, string> = {
    'Clear': '☀️',
    'Partly Cloudy': '⛅️',
    'Cloudy': '☁️',
    'Rain': '🌧',
    'Snow': '🌨',
    'Thunderstorm': '⛈',
    'Windy': '💨'
  };

  function getWeatherIcon(condition: WeatherCondition): string {
    return weatherIcons[condition];
  }

  function updateWeather(location: string): void {
    selectedLocation = location;
    showLocationPicker = false;
    
    if (location === 'London') {
      currentWeather = {
        temp: 65,
        feelsLike: 63,
        humidity: 75,
        windSpeed: 12,
        condition: 'Rain',
        icon: '🌧'
      };
    } else if (location === 'Tokyo') {
      currentWeather = {
        temp: 78,
        feelsLike: 80,
        humidity: 70,
        windSpeed: 5,
        condition: 'Partly Cloudy',
        icon: '⛅️'
      };
    }
  }

  let activeTab: 'hourly' | 'daily' = 'hourly';
</script>

<div class="weather-app">
  <div class="location-bar">
    <button 
      class="location-button" 
      on:click={() => showLocationPicker = !showLocationPicker}
      aria-expanded={showLocationPicker}
      aria-controls="location-picker"
    >
      <span class="icon" aria-hidden="true">📍</span>
      <span>{selectedLocation}</span>
      <span class="arrow" aria-hidden="true">▼</span>
    </button>
    
    {#if showLocationPicker}
      <div 
        id="location-picker"
        class="location-picker" 
        transition:slide
        role="listbox"
        aria-label="Select location"
      >
        {#each locations as location, i}
          <button 
            class="location-option"
            class:active={location === selectedLocation}
            on:click={() => updateWeather(location)}
            role="option"
            aria-selected={location === selectedLocation}
            style="animation: slideIn {i * 0.1}s ease-out"
          >
            <span class="icon" aria-hidden="true">📍</span>
            {location}
          </button>
        {/each}
      </div>
    {/if}
  </div>

  <div class="current-weather" role="region" aria-label="Current weather">
    <div class="main-info">
      <span class="temperature">{currentWeather.temp}°</span>
      <span class="condition">
        <span class="icon" aria-hidden="true">{currentWeather.icon}</span>
        {currentWeather.condition}
      </span>
    </div>
    
    <div class="details">
      <div class="detail">
        <span class="label">Feels like</span>
        <span class="value">{currentWeather.feelsLike}°</span>
      </div>
      <div class="detail">
        <span class="label">Humidity</span>
        <span class="value">{currentWeather.humidity}%</span>
      </div>
      <div class="detail">
        <span class="label">Wind</span>
        <span class="value">{currentWeather.windSpeed} mph</span>
      </div>
    </div>
  </div>

  {#if weatherAlerts.length > 0}
    <div class="weather-alerts" role="alert">
      {#each weatherAlerts as alert}
        <div class="alert" transition:slide>
          <div class="alert-header">
            <span class="alert-type">⚠️ {alert.type}</span>
            <span class="alert-time">{alert.time}</span>
          </div>
          <p class="alert-description">{alert.description}</p>
        </div>
      {/each}
    </div>
  {/if}

  <div class="forecast-tabs" role="tablist">
    <button 
      class="tab-button" 
      class:active={activeTab === 'hourly'}
      on:click={() => activeTab = 'hourly'}
      role="tab"
      aria-selected={activeTab === 'hourly'}
      aria-controls="hourly-panel"
    >
      Hourly
    </button>
    <button 
      class="tab-button" 
      class:active={activeTab === 'daily'}
      on:click={() => activeTab = 'daily'}
      role="tab"
      aria-selected={activeTab === 'daily'}
      aria-controls="daily-panel"
    >
      Daily
    </button>
  </div>

  {#if activeTab === 'hourly'}
    <div 
      id="hourly-panel"
      class="hourly-forecast" 
      role="tabpanel"
      aria-label="Hourly forecast"
      transition:slide
    >
      {#each hourlyForecast as hour, i}
        <div 
          class="hour"
          style="animation: slideIn {i * 0.1}s ease-out"
        >
          <span class="time">{hour.time}</span>
          <span class="icon" aria-hidden="true">{hour.icon}</span>
          <span class="temp">{hour.temp}°</span>
        </div>
      {/each}
    </div>
  {:else}
    <div 
      id="daily-panel"
      class="daily-forecast" 
      role="tabpanel"
      aria-label="Daily forecast"
      transition:slide
    >
      {#each forecast as day, i}
        <div 
          class="day"
          style="animation: slideIn {i * 0.1}s ease-out"
        >
          <span class="name">{day.day}</span>
          <span class="icon" aria-hidden="true">{day.icon}</span>
          <div class="temps">
            <span class="high">{day.high}°</span>
            <span class="low">{day.low}°</span>
          </div>
          <span class="precipitation">{day.precipitation}</span>
        </div>
      {/each}
    </div>
  {/if}
</div>

<style>
  .weather-app {
    height: 100%;
    background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
    color: white;
    display: flex;
    flex-direction: column;
    gap: 20px;
    padding: 20px;
    padding-bottom: 80px; /* Add padding for navigation bar */
    overflow-y: auto; /* Make the content scrollable */
  }

  .location-bar {
    position: relative;
  }

  .location-button {
    width: 100%;
    padding: 15px;
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 15px;
    color: white;
    display: flex;
    align-items: center;
    gap: 10px;
    cursor: pointer;
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    transition: all 0.2s ease;
  }

  .location-button:hover {
    background: rgba(255, 255, 255, 0.15);
    border-color: rgba(255, 255, 255, 0.2);
    transform: translateY(-2px);
  }

  .location-picker {
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    margin-top: 10px;
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 15px;
    overflow: hidden;
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    z-index: 100;
  }

  .location-option {
    width: 100%;
    padding: 15px;
    background: none;
    border: none;
    color: white;
    display: flex;
    align-items: center;
    gap: 10px;
    cursor: pointer;
    transition: all 0.2s ease;
  }

  .location-option:hover {
    background: rgba(255, 255, 255, 0.1);
  }

  .location-option.active {
    background: rgba(255, 255, 255, 0.2);
  }

  .current-weather {
    text-align: center;
    padding: 20px;
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 20px;
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
  }

  .temperature {
    font-size: 72px;
    font-weight: 200;
    line-height: 1;
    margin-bottom: 10px;
    display: block;
  }

  .condition {
    font-size: 24px;
    opacity: 0.8;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 10px;
  }

  .details {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
    margin-top: 30px;
    padding-top: 20px;
    border-top: 1px solid rgba(255, 255, 255, 0.1);
  }

  .detail {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }

  .label {
    font-size: 14px;
    opacity: 0.6;
  }

  .value {
    font-size: 18px;
    font-weight: 500;
  }

  .weather-alerts {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .alert {
    padding: 15px;
    background: rgba(255, 59, 48, 0.2);
    border: 1px solid rgba(255, 59, 48, 0.3);
    border-radius: 15px;
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
  }

  .alert-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
  }

  .alert-type {
    font-weight: 600;
  }

  .alert-time {
    font-size: 14px;
    opacity: 0.8;
  }

  .alert-description {
    margin: 0;
    font-size: 14px;
    line-height: 1.4;
  }

  .forecast-tabs {
    display: flex;
    gap: 10px;
    padding: 5px;
    background: rgba(255, 255, 255, 0.1);
    border-radius: 15px;
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
  }

  .tab-button {
    flex: 1;
    padding: 10px;
    border: none;
    background: none;
    color: white;
    border-radius: 10px;
    cursor: pointer;
    transition: all 0.2s ease;
  }

  .tab-button.active {
    background: rgba(255, 255, 255, 0.2);
  }

  .hourly-forecast, .daily-forecast {
    display: flex;
    gap: 15px;
    overflow-x: auto;
    padding: 10px 0;
    scrollbar-width: none;
    -ms-overflow-style: none;
  }

  .hourly-forecast::-webkit-scrollbar, 
  .daily-forecast::-webkit-scrollbar {
    display: none;
  }

  .hour, .day {
    flex: 0 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    padding: 15px;
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 15px;
    min-width: 100px;
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    transition: all 0.2s ease;
  }

  .hour:hover, .day:hover {
    transform: translateY(-2px);
    background: rgba(255, 255, 255, 0.15);
    border-color: rgba(255, 255, 255, 0.2);
  }

  .temps {
    display: flex;
    gap: 10px;
  }

  .high {
    color: #ff9f0a;
  }

  .low {
    opacity: 0.7;
  }

  .precipitation {
    font-size: 14px;
    opacity: 0.7;
  }

  @keyframes slideIn {
    from {
      opacity: 0;
      transform: translateX(20px);
    }
    to {
      opacity: 1;
      transform: translateX(0);
    }
  }

  /* Add glass morphism hover effect */
  .location-button, .hour, .day, .alert {
    position: relative;
    overflow: hidden;
  }

  .location-button::after,
  .hour::after,
  .day::after,
  .alert::after {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    width: 100%;
    height: 100%;
    background: radial-gradient(circle, rgba(255,255,255,0.1) 0%, rgba(255,255,255,0) 70%);
    transform: translate(-50%, -50%) scale(0);
    transition: transform 0.3s ease;
    pointer-events: none;
  }

  .location-button:hover::after,
  .hour:hover::after,
  .day:hover::after,
  .alert:hover::after {
    transform: translate(-50%, -50%) scale(2);
  }
</style>
