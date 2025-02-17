<script lang="ts">
  import { slide } from 'svelte/transition';

  interface Song {
    title: string;
    artist: string;
    albumArt: string;
  }

  let currentSong: Song = {
    title: "Sample Song",
    artist: "Sample Artist",
    albumArt: "🎵"
  };

  let isPlaying = false;
  let shuffle = false;
  let repeat = false;
  let progress = 0;
  let currentTime = 0;
  let duration = 180; // 3 minutes in seconds

  const playlist: Song[] = [
    { title: "Song 1", artist: "Artist 1", albumArt: "🎵" },
    { title: "Song 2", artist: "Artist 2", albumArt: "🎵" },
    { title: "Song 3", artist: "Artist 3", albumArt: "🎵" }
  ];

  function formatTime(seconds: number): string {
    const minutes = Math.floor(seconds / 60);
    const remainingSeconds = Math.floor(seconds % 60);
    return `${minutes}:${remainingSeconds.toString().padStart(2, '0')}`;
  }

  function togglePlay(): void {
    isPlaying = !isPlaying;
  }

  function toggleShuffle(): void {
    shuffle = !shuffle;
  }

  function toggleRepeat(): void {
    repeat = !repeat;
  }

  function previousSong(): void {
    const currentIndex = playlist.indexOf(currentSong);
    const newIndex = currentIndex > 0 ? currentIndex - 1 : playlist.length - 1;
    currentSong = playlist[newIndex];
  }

  function nextSong(): void {
    const currentIndex = playlist.indexOf(currentSong);
    const newIndex = currentIndex < playlist.length - 1 ? currentIndex + 1 : 0;
    currentSong = playlist[newIndex];
  }

  function playSong(song: Song): void {
    currentSong = song;
    isPlaying = true;
  }
</script>

<div class="music-app">
  <div class="now-playing" role="region" aria-label="Now playing">
    <div class="album-art" style="background-image: url({currentSong.albumArt})">
      <div class="reflection"></div>
    </div>
    
    <div class="song-info">
      <h2 class="title">{currentSong.title}</h2>
      <p class="artist">{currentSong.artist}</p>
    </div>

    <div class="progress-bar" role="progressbar" aria-valuenow={progress} aria-valuemin="0" aria-valuemax="100">
      <div class="progress" style="width: {progress}%"></div>
      <div class="progress-handle" style="left: {progress}%"></div>
      <div class="time-info">
        <span class="current-time">{formatTime(currentTime)}</span>
        <span class="duration">{formatTime(duration)}</span>
      </div>
    </div>

    <div class="controls" role="toolbar" aria-label="Playback controls">
      <button 
        class="control-button shuffle"
        on:click={toggleShuffle}
        aria-pressed={shuffle}
        aria-label="Shuffle"
      >
        🔀
      </button>
      
      <button 
        class="control-button previous"
        on:click={previousSong}
        aria-label="Previous song"
      >
        ⏮️
      </button>
      
      <button 
        class="control-button play-pause"
        on:click={togglePlay}
        aria-label={isPlaying ? 'Pause' : 'Play'}
      >
        {isPlaying ? '⏸️' : '▶️'}
      </button>
      
      <button 
        class="control-button next"
        on:click={nextSong}
        aria-label="Next song"
      >
        ⏭️
      </button>
      
      <button 
        class="control-button repeat"
        on:click={toggleRepeat}
        aria-pressed={repeat}
        aria-label="Repeat"
      >
        🔁
      </button>
    </div>
  </div>

  <div class="playlist" role="list" aria-label="Playlist">
    {#each playlist as song, i}
      <div 
        class="song-item"
        class:active={song === currentSong}
        role="listitem"
        style="animation: slideIn {i * 0.1}s ease-out"
      >
        <button
          class="song-button"
          on:click={() => playSong(song)}
          aria-label="Play {song.title} by {song.artist}"
        >
          <div class="song-art" style="background-image: url({song.albumArt})"></div>
          <div class="song-details">
            <span class="song-title">{song.title}</span>
            <span class="song-artist">{song.artist}</span>
          </div>
          {#if song === currentSong && isPlaying}
            <div class="playing-indicator">
              <span class="bar"></span>
              <span class="bar"></span>
              <span class="bar"></span>
            </div>
          {/if}
        </button>
      </div>
    {/each}
  </div>
</div>

<style>
  .music-app {
    height: 100%;
    background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
    color: white;
    display: flex;
    flex-direction: column;
    gap: 30px;
    padding: 20px;
    padding-bottom: 80px;
    overflow-y: auto;
  }

  .now-playing {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 20px;
    padding: 20px;
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 20px;
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
  }

  .album-art {
    width: 200px;
    height: 200px;
    border-radius: 20px;
    background-size: cover;
    background-position: center;
    position: relative;
    overflow: hidden;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
    transform: translateY(0);
    transition: transform 0.3s ease;
  }

  .album-art:hover {
    transform: translateY(-5px);
  }

  .reflection {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 100%;
    background: linear-gradient(
      to bottom,
      rgba(255, 255, 255, 0.1),
      rgba(255, 255, 255, 0.05) 50%,
      transparent
    );
    transform: translateY(-50%);
    animation: shine 5s infinite;
  }

  .song-info {
    text-align: center;
  }

  .title {
    font-size: 24px;
    font-weight: 600;
    margin: 0;
    margin-bottom: 5px;
  }

  .artist {
    font-size: 16px;
    opacity: 0.8;
    margin: 0;
  }

  .progress-bar {
    width: 100%;
    height: 4px;
    background: rgba(255, 255, 255, 0.1);
    border-radius: 2px;
    position: relative;
    cursor: pointer;
  }

  .progress {
    height: 100%;
    background: #007AFF;
    border-radius: 2px;
    transition: width 0.1s linear;
  }

  .progress-handle {
    position: absolute;
    top: 50%;
    width: 12px;
    height: 12px;
    background: white;
    border-radius: 50%;
    transform: translate(-50%, -50%);
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
    transition: transform 0.2s ease;
  }

  .progress-handle:hover {
    transform: translate(-50%, -50%) scale(1.2);
  }

  .time-info {
    display: flex;
    justify-content: space-between;
    font-size: 12px;
    opacity: 0.8;
    margin-top: 5px;
  }

  .controls {
    display: flex;
    align-items: center;
    gap: 20px;
  }

  .control-button {
    background: none;
    border: none;
    color: white;
    font-size: 24px;
    cursor: pointer;
    transition: all 0.2s ease;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba(255, 255, 255, 0.1);
  }

  .control-button:hover {
    transform: scale(1.1);
    background: rgba(255, 255, 255, 0.2);
  }

  .control-button:active {
    transform: scale(0.95);
  }

  .control-button.play-pause {
    width: 50px;
    height: 50px;
    font-size: 28px;
    background: #007AFF;
  }

  .control-button.play-pause:hover {
    background: #0066CC;
  }

  .playlist {
    flex: 1;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    gap: 10px;
    padding: 10px;
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 20px;
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
  }

  .song-item {
    position: relative;
    border-radius: 10px;
    transition: all 0.2s ease;
  }

  .song-button {
    width: 100%;
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 10px;
    background: none;
    border: none;
    color: white;
    cursor: pointer;
    text-align: left;
  }

  .song-item:hover {
    background: rgba(255, 255, 255, 0.1);
    transform: translateX(5px);
  }

  .song-item.active {
    background: rgba(255, 255, 255, 0.15);
  }

  .song-art {
    width: 50px;
    height: 50px;
    border-radius: 10px;
    background-size: cover;
    background-position: center;
  }

  .song-details {
    flex: 1;
    text-align: left;
    display: flex;
    flex-direction: column;
    gap: 5px;
  }

  .song-title {
    font-weight: 500;
  }

  .song-artist {
    font-size: 14px;
    opacity: 0.8;
  }

  .playing-indicator {
    display: flex;
    align-items: flex-end;
    gap: 2px;
    height: 20px;
  }

  .bar {
    width: 3px;
    height: 100%;
    background: #007AFF;
    border-radius: 1.5px;
    animation: bounce 0.8s ease infinite;
  }

  .bar:nth-child(2) {
    animation-delay: 0.2s;
  }

  .bar:nth-child(3) {
    animation-delay: 0.4s;
  }

  @keyframes bounce {
    0%, 100% {
      height: 20%;
    }
    50% {
      height: 100%;
    }
  }

  @keyframes shine {
    0% {
      transform: translateY(-100%);
    }
    100% {
      transform: translateY(100%);
    }
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

  /* Scrollbar styling */
  .playlist::-webkit-scrollbar {
    width: 8px;
  }

  .playlist::-webkit-scrollbar-track {
    background: rgba(255, 255, 255, 0.1);
    border-radius: 4px;
  }

  .playlist::-webkit-scrollbar-thumb {
    background: rgba(255, 255, 255, 0.2);
    border-radius: 4px;
  }

  .playlist::-webkit-scrollbar-thumb:hover {
    background: rgba(255, 255, 255, 0.3);
  }
</style>
