<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  export let isOpen = false;
  
  let brightness = 80;
  let volume = 50;
  let isWifiOn = true;
  let isBluetoothOn = false;
  let isAirplaneMode = false;
  let isRotationLocked = false;
  let isSilent = false;
  let isDarkMode = false;

  let startY = 0;
  let currentY = 0;
  let isDragging = false;
  let dragProgress = 0;

  function handleTouchStart(e) {
    startY = e.touches[0].clientY;
    isDragging = true;
  }

  function handleTouchMove(e) {
    if (!isDragging) return;
    currentY = e.touches[0].clientY;
    dragProgress = Math.max(0, Math.min(100, ((currentY - startY) / 200) * 100));
    
    if (!isOpen && dragProgress > 80) {
      isOpen = true;
      dispatch('open');
    } else if (isOpen && dragProgress > 50) {
      isOpen = false;
      dispatch('close');
    }
  }

  function handleTouchEnd() {
    isDragging = false;
    dragProgress = 0;
  }

  function toggleSetting(setting) {
    switch(setting) {
      case 'wifi':
        isWifiOn = !isWifiOn;
        break;
      case 'bluetooth':
        isBluetoothOn = !isBluetoothOn;
        break;
      case 'airplane':
        isAirplaneMode = !isAirplaneMode;
        break;
      case 'rotation':
        isRotationLocked = !isRotationLocked;
        break;
      case 'silent':
        isSilent = !isSilent;
        break;
      case 'darkMode':
        isDarkMode = !isDarkMode;
        dispatch('darkModeChange', isDarkMode);
        break;
    }
  }
</script>

<div class="control-center" 
  class:open={isOpen}
  on:touchstart={handleTouchStart}
  on:touchmove={handleTouchMove}
  on:touchend={handleTouchEnd}
>
  <div class="controls-grid">
    <div class="control-group">
      <div class="control-row">
        <button 
          class="control-button large" 
          class:active={isWifiOn}
          on:click={() => toggleSetting('wifi')}
        >
          <span class="icon">📡</span>
          <span class="label">Wi-Fi</span>
        </button>
        <button 
          class="control-button large" 
          class:active={isBluetoothOn}
          on:click={() => toggleSetting('bluetooth')}
        >
          <span class="icon">📶</span>
          <span class="label">Bluetooth</span>
        </button>
      </div>
      <div class="control-row">
        <button 
          class="control-button large" 
          class:active={isAirplaneMode}
          on:click={() => toggleSetting('airplane')}
        >
          <span class="icon">✈️</span>
          <span class="label">Airplane Mode</span>
        </button>
        <button 
          class="control-button large" 
          class:active={isSilent}
          on:click={() => toggleSetting('silent')}
        >
          <span class="icon">{isSilent ? '🔇' : '🔊'}</span>
          <span class="label">Silent Mode</span>
        </button>
      </div>
    </div>

    <div class="slider-controls">
      <div class="slider-control">
        <span class="icon">☀️</span>
        <input 
          type="range" 
          min="0" 
          max="100" 
          bind:value={brightness}
        />
      </div>
      <div class="slider-control">
        <span class="icon">🔊</span>
        <input 
          type="range" 
          min="0" 
          max="100" 
          bind:value={volume}
        />
      </div>
    </div>

    <div class="media-controls">
      <div class="now-playing">
        <div class="media-info">
          <span class="song-title">Currently Playing</span>
          <span class="artist">Artist Name</span>
        </div>
        <div class="media-buttons">
          <button class="media-button">⏮️</button>
          <button class="media-button large">⏯️</button>
          <button class="media-button">⏭️</button>
        </div>
      </div>
    </div>

    <div class="quick-actions">
      <button 
        class="control-button"
        class:active={isRotationLocked}
        on:click={() => toggleSetting('rotation')}
      >
        <span class="icon">🔄</span>
      </button>
      <button 
        class="control-button"
        class:active={isDarkMode}
        on:click={() => toggleSetting('darkMode')}
      >
        <span class="icon">{isDarkMode ? '🌙' : '☀️'}</span>
      </button>
      <button class="control-button">
        <span class="icon">📸</span>
      </button>
      <button class="control-button">
        <span class="icon">🔦</span>
      </button>
    </div>
  </div>
</div>

<style>
  .control-center {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.8);
    backdrop-filter: blur(20px);
    transform: translateY(-100%);
    transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    z-index: 900;
    color: white;
    padding: 20px;
  }

  .control-center.open {
    transform: translateY(0);
  }

  .controls-grid {
    display: flex;
    flex-direction: column;
    gap: 20px;
  }

  .control-group {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .control-row {
    display: flex;
    gap: 10px;
  }

  .control-button {
    background: rgba(255, 255, 255, 0.1);
    border: none;
    border-radius: 12px;
    color: white;
    padding: 12px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 5px;
    flex: 1;
    cursor: pointer;
    transition: all 0.2s;
  }

  .control-button.large {
    padding: 20px;
  }

  .control-button.active {
    background: #007AFF;
  }

  .control-button:active {
    transform: scale(0.95);
  }

  .icon {
    font-size: 24px;
  }

  .label {
    font-size: 12px;
    opacity: 0.8;
  }

  .slider-controls {
    display: flex;
    flex-direction: column;
    gap: 15px;
  }

  .slider-control {
    display: flex;
    align-items: center;
    gap: 10px;
    background: rgba(255, 255, 255, 0.1);
    padding: 10px;
    border-radius: 12px;
  }

  input[type="range"] {
    flex: 1;
    height: 4px;
    -webkit-appearance: none;
    background: rgba(255, 255, 255, 0.2);
    border-radius: 2px;
  }

  input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 18px;
    height: 18px;
    background: white;
    border-radius: 50%;
    cursor: pointer;
  }

  .media-controls {
    background: rgba(255, 255, 255, 0.1);
    border-radius: 12px;
    padding: 15px;
  }

  .now-playing {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .media-info {
    display: flex;
    flex-direction: column;
  }

  .song-title {
    font-weight: 600;
  }

  .artist {
    font-size: 12px;
    opacity: 0.8;
  }

  .media-buttons {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 20px;
  }

  .media-button {
    background: none;
    border: none;
    color: white;
    font-size: 20px;
    cursor: pointer;
    padding: 5px;
  }

  .media-button.large {
    font-size: 30px;
  }

  .quick-actions {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
  }
</style>
