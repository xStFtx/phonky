<script lang="ts">
  let currentTime = new Date();
  let timerId: number;

  function updateTime() {
    currentTime = new Date();
  }

  $: timeString = currentTime.toLocaleTimeString('en-US', {
    hour: 'numeric',
    minute: '2-digit',
    second: '2-digit',
    hour12: true
  });

  $: dateString = currentTime.toLocaleDateString('en-US', {
    weekday: 'long',
    month: 'long',
    day: 'numeric'
  });

  onMount(() => {
    timerId = setInterval(updateTime, 1000);
    return () => clearInterval(timerId);
  });
</script>

<div class="h-full bg-black text-white flex flex-col">
  <div class="h-12 px-4 flex items-center justify-between border-b border-white/10">
    <h1 class="text-lg font-medium">Clock</h1>
  </div>

  <div class="flex-1 flex flex-col items-center justify-center gap-4">
    <div class="text-6xl font-light">{timeString}</div>
    <div class="text-lg text-white/60">{dateString}</div>
  </div>
</div>

<script>
  import { onMount } from 'svelte';
</script>
