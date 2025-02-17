<script lang="ts">
  import { fade, slide, scale, fly } from 'svelte/transition';
  import { elasticOut, quintOut } from 'svelte/easing';
  import LockScreen from '$lib/components/LockScreen.svelte';
  import NotificationCenter from '$lib/components/NotificationCenter.svelte';
  import ControlCenter from '$lib/components/ControlCenter.svelte';
  import Weather from '$lib/components/apps/Weather.svelte';
  import Music from '$lib/components/apps/Music.svelte';
  import Calculator from '$lib/components/apps/Calculator.svelte';
  import Notes from '$lib/components/apps/Notes.svelte';
  import Messages from '$lib/components/apps/Messages.svelte';
  import Camera from '$lib/components/apps/Camera.svelte';

  interface App {
    name: string;
    icon: string;
    component: any;
    gradient: string;
  }

  interface WeatherData {
    temp: string;
    condition: string;
    high: string;
    low: string;
    icon: string;
  }

  interface Notification {
    id: string;
    title: string;
    message: string;
    timestamp: string;
  }

  const apps: App[] = [
    { name: 'Weather', icon: '🌤️', component: Weather, gradient: 'from-sky-400 to-blue-600' },
    { name: 'Music', icon: '🎵', component: Music, gradient: 'from-pink-500 to-purple-600' },
    { name: 'Calculator', icon: '🔢', component: Calculator, gradient: 'from-gray-700 to-gray-900' },
    { name: 'Notes', icon: '📝', component: Notes, gradient: 'from-yellow-400 to-orange-500' },
    { name: 'Messages', icon: '💬', component: Messages, gradient: 'from-green-400 to-emerald-600' },
    { name: 'Camera', icon: '📸', component: Camera, gradient: 'from-indigo-400 to-blue-600' }
  ];

  let isLocked = false;
  let selectedApp: App | null = null;
  let showNotificationCenter = false;
  let showControlCenter = false;
  let isNotificationCenterOpen = false;
  let isControlCenterOpen = false;
  let notifications: Notification[] = [];
  let appTransition = false;
  
  let time = new Date().toLocaleTimeString('en-US', {
    hour: 'numeric',
    minute: '2-digit',
    hour12: true
  });

  setInterval(() => {
    time = new Date().toLocaleTimeString('en-US', {
      hour: 'numeric',
      minute: '2-digit',
      hour12: true
    });
  }, 1000);

  let weatherData: WeatherData = {
    temp: "72°",
    condition: "Sunny",
    high: "75°",
    low: "65°",
    icon: "☀️"
  };

  let isDarkMode = false;

  function handleUnlock() {
    isLocked = false;
  }

  function handleAppClick(app: App) {
    appTransition = true;
    setTimeout(() => {
      selectedApp = app;
      appTransition = false;
    }, 300);
  }

  function closeApp() {
    appTransition = true;
    setTimeout(() => {
      selectedApp = null;
      appTransition = false;
    }, 300);
  }

  function handleDarkModeChange(event: CustomEvent) {
    isDarkMode = event.detail;
  }

  function handleBackClick() {
    selectedApp = null;
  }

  function handleHomeClick() {
    selectedApp = null;
    isNotificationCenterOpen = false;
    isControlCenterOpen = false;
  }

  function handleMouseDown(event: MouseEvent): void {
    // Add mouse handling if needed
  }

  function handleDragStart(event: DragEvent): void {
    event.preventDefault();
  }

  function handleTouchStart(event: TouchEvent): void {
    // Add touch handling if needed
  }

  function handleTouchMove(event: TouchEvent): void {
    // Add touch handling if needed
  }

  function handleTouchEnd(event: TouchEvent): void {
    // Add touch handling if needed
  }
</script>

<div class="h-screen w-full bg-gradient-to-br from-gray-900 to-black overflow-hidden relative flex items-center justify-center">
  <div class="relative w-[400px] h-[800px]">
    <!-- Phone Frame -->
    <div class="absolute inset-0 bg-black rounded-[60px] shadow-2xl shadow-blue-500/10">
      <!-- Status Bar -->
      <div class="absolute top-0 left-0 right-0 h-6 flex items-center justify-between px-8 pt-3 z-50">
        <span class="text-white/90 text-sm">{time}</span>
        <div class="flex items-center gap-1">
          <span class="text-white/90">📶</span>
          <span class="text-white/90">🔋</span>
        </div>
      </div>

      <!-- Dynamic Island -->
      <div 
        class="absolute top-1 left-1/2 -translate-x-1/2 w-[120px] h-[32px] bg-black rounded-full z-50 flex items-center justify-center gap-2 {selectedApp ? 'scale-150' : ''} transition-transform duration-300"
      >
        {#if selectedApp}
          <span class="text-white/90 text-xs" transition:fade>{selectedApp.name}</span>
        {/if}
      </div>

      <!-- Main Content -->
      <div class="absolute inset-0 mt-12 rounded-[60px] overflow-hidden bg-gradient-to-br from-gray-900 via-gray-800 to-black">
        {#if isLocked}
          <LockScreen 
            on:unlock={handleUnlock}
            weather={{
              temp: weatherData.temp,
              condition: weatherData.condition,
              high: weatherData.high,
              low: weatherData.low,
              icon: weatherData.icon
            }}
          />
        {:else if selectedApp}
          <div 
            class="h-full"
            in:fly={{y: 50, duration: 300, delay: 300}}
            out:fly={{y: -50, duration: 300}}
          >
            <svelte:component this={selectedApp.component} />
          </div>
        {:else}
          <!-- App Grid -->
          <div class="h-full p-6 pt-12" in:fade={{duration: 200}}>
            <div class="grid grid-cols-3 gap-4" class:blur-sm={appTransition}>
              {#each apps as app, i}
                <button 
                  class="aspect-square flex flex-col items-center justify-center gap-1 text-white hover:scale-110 transition-all duration-300 ease-out"
                  on:click={() => handleAppClick(app)}
                  in:scale={{
                    duration: 400,
                    delay: i * 100,
                    easing: elasticOut
                  }}
                >
                  <div class="w-16 h-16 bg-gradient-to-br {app.gradient} rounded-2xl flex items-center justify-center text-2xl shadow-lg shadow-black/50 hover:shadow-xl hover:shadow-black/60 transition-shadow">
                    {app.icon}
                  </div>
                  <span class="text-xs font-medium">{app.name}</span>
                </button>
              {/each}
            </div>
          </div>
        {/if}

        <!-- Home Indicator -->
        <div class="absolute bottom-1 left-1/2 -translate-x-1/2 w-32 h-1 bg-white/20 rounded-full"></div>

        <!-- Back Button when app is open -->
        {#if selectedApp}
          <button
            class="absolute bottom-4 left-1/2 -translate-x-1/2 w-12 h-12 rounded-full bg-white/10 backdrop-blur-md flex items-center justify-center hover:bg-white/20 transition-colors"
            on:click={closeApp}
            in:fade
          >
            ←
          </button>
        {/if}
      </div>
    </div>
  </div>
</div>

<style>
  @keyframes float {
    0%, 100% {
      transform: translateY(0);
    }
    50% {
      transform: translateY(-10px);
    }
  }

  .float {
    animation: float 6s ease-in-out infinite;
  }
</style>
