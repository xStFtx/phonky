<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  export let notifications = [];
  export let isOpen = false;
  export let weather = {
    temp: '72°',
    condition: '☀️ Sunny',
    high: '75°',
    low: '65°'
  };

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

  function clearNotification(index) {
    notifications = notifications.filter((_, i) => i !== index);
  }

  function clearAllNotifications() {
    notifications = [];
  }
</script>

<div class="notification-center" 
  class:open={isOpen}
  on:touchstart={handleTouchStart}
  on:touchmove={handleTouchMove}
  on:touchend={handleTouchEnd}
>
  <div class="weather-widget">
    <div class="current-weather">
      <span class="temp">{weather.temp}</span>
      <span class="condition">{weather.condition}</span>
    </div>
    <div class="high-low">
      <span>H: {weather.high}</span>
      <span>L: {weather.low}</span>
    </div>
  </div>

  <div class="notifications-container">
    {#if notifications.length > 0}
      <div class="notifications-header">
        <h3>Notifications</h3>
        <button on:click={clearAllNotifications}>Clear All</button>
      </div>
      {#each notifications as notification, i (i)}
        <div class="notification" animate:flip>
          <div class="notification-icon">
            {notification.icon}
          </div>
          <div class="notification-content">
            <div class="notification-title">{notification.title}</div>
            <div class="notification-text">{notification.text}</div>
            <div class="notification-time">{notification.time}</div>
          </div>
          <button class="clear-notification" on:click={() => clearNotification(i)}>
            ×
          </button>
        </div>
      {/each}
    {:else}
      <div class="no-notifications">
        <p>No New Notifications</p>
      </div>
    {/if}
  </div>
</div>

<style>
  .notification-center {
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
    overflow-y: auto;
  }

  .notification-center.open {
    transform: translateY(0);
  }

  .weather-widget {
    background: rgba(255, 255, 255, 0.1);
    border-radius: 15px;
    padding: 15px;
    margin-bottom: 20px;
  }

  .current-weather {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 10px;
  }

  .temp {
    font-size: 36px;
    font-weight: 300;
  }

  .condition {
    font-size: 20px;
  }

  .high-low {
    display: flex;
    gap: 15px;
    opacity: 0.8;
  }

  .notifications-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 15px;
  }

  .notifications-header button {
    background: none;
    border: none;
    color: #007AFF;
    font-size: 14px;
    cursor: pointer;
  }

  .notification {
    background: rgba(255, 255, 255, 0.1);
    border-radius: 12px;
    padding: 12px;
    margin-bottom: 10px;
    display: flex;
    align-items: flex-start;
    gap: 12px;
    animation: slideIn 0.3s ease-out;
  }

  .notification-icon {
    font-size: 24px;
    min-width: 40px;
    height: 40px;
    background: rgba(255, 255, 255, 0.1);
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .notification-content {
    flex: 1;
  }

  .notification-title {
    font-weight: 600;
    margin-bottom: 4px;
  }

  .notification-text {
    opacity: 0.8;
    font-size: 14px;
    margin-bottom: 4px;
  }

  .notification-time {
    font-size: 12px;
    opacity: 0.6;
  }

  .clear-notification {
    background: none;
    border: none;
    color: white;
    font-size: 20px;
    opacity: 0.6;
    cursor: pointer;
    padding: 0 5px;
  }

  .clear-notification:hover {
    opacity: 1;
  }

  .no-notifications {
    text-align: center;
    padding: 40px 0;
    opacity: 0.6;
  }

  @keyframes slideIn {
    from {
      transform: translateX(-100%);
      opacity: 0;
    }
    to {
      transform: translateX(0);
      opacity: 1;
    }
  }
</style>
