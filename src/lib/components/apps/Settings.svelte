<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  
  const dispatch = createEventDispatcher();
  
  let isDarkMode = true;
  let isAirplaneMode = false;
  let isBluetooth = true;
  let isWifi = true;
  let brightness = 80;
  let volume = 50;

  function handleDarkModeChange() {
    isDarkMode = !isDarkMode;
    dispatch('darkModeChange', isDarkMode);
  }
</script>

<div class="h-full bg-black text-white flex flex-col">
  <div class="h-12 px-4 flex items-center justify-between border-b border-white/10">
    <h1 class="text-lg font-medium">Settings</h1>
  </div>

  <div class="flex-1 overflow-y-auto">
    <div class="p-4 space-y-6">
      <!-- Display & Brightness -->
      <div class="space-y-4">
        <h2 class="text-sm font-medium text-white/60">Display & Brightness</h2>
        <div class="space-y-4">
          <label class="flex items-center justify-between">
            <span>Dark Mode</span>
            <button 
              class="w-12 h-6 rounded-full relative transition-colors duration-200 ease-in-out focus:outline-none"
              class:bg-blue-500={isDarkMode}
              class:bg-gray-600={!isDarkMode}
              on:click={handleDarkModeChange}
            >
              <span
                class="w-5 h-5 bg-white rounded-full absolute transform transition-transform duration-200 ease-in-out"
                class:translate-x-6={isDarkMode}
                class:translate-x-1={!isDarkMode}
              />
            </button>
          </label>
          
          <div>
            <span class="block mb-2">Brightness</span>
            <input 
              type="range" 
              bind:value={brightness}
              min="0"
              max="100"
              class="w-full"
            />
          </div>
        </div>
      </div>

      <!-- Sound -->
      <div class="space-y-4">
        <h2 class="text-sm font-medium text-white/60">Sound</h2>
        <div>
          <span class="block mb-2">Volume</span>
          <input 
            type="range" 
            bind:value={volume}
            min="0"
            max="100"
            class="w-full"
          />
        </div>
      </div>

      <!-- Connections -->
      <div class="space-y-4">
        <h2 class="text-sm font-medium text-white/60">Connections</h2>
        <div class="space-y-4">
          <label class="flex items-center justify-between">
            <span>Airplane Mode</span>
            <button 
              class="w-12 h-6 rounded-full relative transition-colors duration-200 ease-in-out focus:outline-none"
              class:bg-blue-500={isAirplaneMode}
              class:bg-gray-600={!isAirplaneMode}
              on:click={() => isAirplaneMode = !isAirplaneMode}
            >
              <span
                class="w-5 h-5 bg-white rounded-full absolute transform transition-transform duration-200 ease-in-out"
                class:translate-x-6={isAirplaneMode}
                class:translate-x-1={!isAirplaneMode}
              />
            </button>
          </label>

          <label class="flex items-center justify-between">
            <span>Wi-Fi</span>
            <button 
              class="w-12 h-6 rounded-full relative transition-colors duration-200 ease-in-out focus:outline-none"
              class:bg-blue-500={isWifi}
              class:bg-gray-600={!isWifi}
              on:click={() => isWifi = !isWifi}
            >
              <span
                class="w-5 h-5 bg-white rounded-full absolute transform transition-transform duration-200 ease-in-out"
                class:translate-x-6={isWifi}
                class:translate-x-1={!isWifi}
              />
            </button>
          </label>

          <label class="flex items-center justify-between">
            <span>Bluetooth</span>
            <button 
              class="w-12 h-6 rounded-full relative transition-colors duration-200 ease-in-out focus:outline-none"
              class:bg-blue-500={isBluetooth}
              class:bg-gray-600={!isBluetooth}
              on:click={() => isBluetooth = !isBluetooth}
            >
              <span
                class="w-5 h-5 bg-white rounded-full absolute transform transition-transform duration-200 ease-in-out"
                class:translate-x-6={isBluetooth}
                class:translate-x-1={!isBluetooth}
              />
            </button>
          </label>
        </div>
      </div>
    </div>
  </div>
</div>
