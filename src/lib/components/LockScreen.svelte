<script>
  import { createEventDispatcher, onMount } from 'svelte';
  const dispatch = createEventDispatcher();

  let time = '';
  let date = '';
  let unlockProgress = 0;
  let touchStartY = 0;
  let isDragging = false;

  onMount(() => {
    updateDateTime();
    setInterval(updateDateTime, 1000);
  });

  function updateDateTime() {
    const now = new Date();
    time = now.toLocaleTimeString('en-US', { 
      hour: 'numeric', 
      minute: '2-digit',
      hour12: true 
    });
    date = now.toLocaleDateString('en-US', { 
      weekday: 'long', 
      month: 'long', 
      day: 'numeric' 
    });
  }

  function handleTouchStart(e) {
    e.preventDefault();
    touchStartY = e.touches[0].clientY;
    isDragging = true;
    console.log('Touch start:', touchStartY);
  }

  function handleTouchMove(e) {
    if (!isDragging) return;
    e.preventDefault();
    
    const currentY = e.touches[0].clientY;
    const diff = touchStartY - currentY;
    unlockProgress = Math.max(0, Math.min(100, (diff / 150) * 100));
    console.log('Touch move:', currentY, 'diff:', diff, 'progress:', unlockProgress);

    if (unlockProgress >= 100) {
      console.log('Unlocking!');
      dispatch('unlock');
      isDragging = false;
      unlockProgress = 0;
    }
  }

  function handleTouchEnd(e) {
    e.preventDefault();
    console.log('Touch end');
    isDragging = false;
    unlockProgress = 0;
  }

  function handleMouseDown(e) {
    touchStartY = e.clientY;
    isDragging = true;
    console.log('Mouse down:', touchStartY);
  }

  function handleMouseMove(e) {
    if (!isDragging) return;
    
    const currentY = e.clientY;
    const diff = touchStartY - currentY;
    unlockProgress = Math.max(0, Math.min(100, (diff / 150) * 100));
    console.log('Mouse move:', currentY, 'diff:', diff, 'progress:', unlockProgress);

    if (unlockProgress >= 100) {
      console.log('Unlocking!');
      dispatch('unlock');
      isDragging = false;
      unlockProgress = 0;
    }
  }

  function handleMouseUp() {
    console.log('Mouse up');
    isDragging = false;
    unlockProgress = 0;
  }
</script>

<svelte:window 
  on:mousemove={handleMouseMove}
  on:mouseup={handleMouseUp}
/>

<div class="lock-screen" 
  on:touchstart|preventDefault={handleTouchStart}
  on:touchmove|preventDefault={handleTouchMove}
  on:touchend|preventDefault={handleTouchEnd}
  on:mousedown={handleMouseDown}
>
  <div class="time">{time}</div>
  <div class="date">{date}</div>

  <div class="unlock-hint" style="opacity: {1 - unlockProgress/100}">
    Swipe up to unlock
    <div class="arrow">↑</div>
  </div>

  <div class="unlock-progress-bar">
    <div class="progress" style="height: {unlockProgress}%"></div>
  </div>

  <div class="quick-actions">
    <button class="quick-action">
      <span class="icon">🔦</span>
    </button>
    <button class="quick-action">
      <span class="icon">📷</span>
    </button>
  </div>
</div>

<style>
  .lock-screen {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(180deg, 
      rgba(0, 0, 0, 0) 0%,
      rgba(0, 0, 0, 0.3) 50%,
      rgba(0, 0, 0, 0.7) 100%
    );
    backdrop-filter: blur(10px);
    display: flex;
    flex-direction: column;
    align-items: center;
    color: white;
    z-index: 1000;
    padding: 40px 20px;
    touch-action: none;
    user-select: none;
  }

  .time {
    font-size: 88px;
    font-weight: 200;
    margin-top: 60px;
    text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
    font-feature-settings: "tnum";
    letter-spacing: -2px;
  }

  .date {
    font-size: 24px;
    opacity: 0.9;
    margin-top: 5px;
    text-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
    font-weight: 400;
  }

  .unlock-hint {
    position: absolute;
    bottom: 120px;
    left: 50%;
    transform: translateX(-50%);
    text-align: center;
    font-size: 16px;
    opacity: 0.9;
    transition: opacity 0.3s;
    text-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
  }

  .arrow {
    font-size: 28px;
    animation: bounce 1.5s infinite;
    margin-top: 5px;
    opacity: 0.8;
  }

  .unlock-progress-bar {
    position: absolute;
    bottom: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 3px;
    height: 100px;
    background: rgba(255, 255, 255, 0.2);
    border-radius: 2px;
    overflow: hidden;
  }

  .progress {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    background: white;
    box-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
    transition: height 0.1s;
  }

  .quick-actions {
    position: absolute;
    bottom: 30px;
    display: flex;
    gap: 30px;
  }

  .quick-action {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.2);
    border: none;
    color: white;
    font-size: 20px;
    cursor: pointer;
    transition: all 0.2s;
    backdrop-filter: blur(5px);
  }

  .quick-action:hover {
    background: rgba(255, 255, 255, 0.3);
    transform: scale(1.1);
  }

  .quick-action:active {
    transform: scale(0.95);
  }

  @keyframes bounce {
    0%, 20%, 50%, 80%, 100% {
      transform: translateY(0);
    }
    40% {
      transform: translateY(-10px);
    }
    60% {
      transform: translateY(-5px);
    }
  }
</style>
