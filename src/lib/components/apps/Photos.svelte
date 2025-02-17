<script lang="ts">
  let photos = [
    { id: 1, url: '🏞️', caption: 'Nature' },
    { id: 2, url: '🌅', caption: 'Sunset' },
    { id: 3, url: '🌃', caption: 'City' },
    { id: 4, url: '🏖️', caption: 'Beach' },
    { id: 5, url: '⛰️', caption: 'Mountains' },
    { id: 6, url: '🌸', caption: 'Spring' }
  ];
</script>

<div class="h-full bg-black text-white flex flex-col">
  <div class="h-12 px-4 flex items-center justify-between border-b border-white/10">
    <h1 class="text-lg font-medium">Photos</h1>
    <button class="text-blue-400 text-sm">Select</button>
  </div>

  <div class="flex-1 p-4 overflow-y-auto">
    <div class="grid grid-cols-3 gap-2">
      {#each photos as photo (photo.id)}
        <button 
          class="aspect-square bg-white/5 rounded-lg flex items-center justify-center text-4xl hover:bg-white/10 transition-colors"
          aria-label={photo.caption}
        >
          {photo.url}
        </button>
      {/each}
    </div>
  </div>
</div>
