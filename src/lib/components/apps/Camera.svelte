<script>
  import { onMount, onDestroy } from 'svelte';

  let videoElement;
  let stream;
  let facingMode = 'user'; // or 'environment' for back camera
  let photos = [];

  onMount(async () => {
    await startCamera();
  });

  onDestroy(() => {
    stopCamera();
  });

  async function startCamera() {
    try {
      stream = await navigator.mediaDevices.getUserMedia({
        video: { facingMode },
        audio: false
      });
      videoElement.srcObject = stream;
    } catch (err) {
      console.error('Error accessing camera:', err);
    }
  }

  function stopCamera() {
    if (stream) {
      stream.getTracks().forEach(track => track.stop());
    }
  }

  function switchCamera() {
    stopCamera();
    facingMode = facingMode === 'user' ? 'environment' : 'user';
    startCamera();
  }

  function takePhoto() {
    const canvas = document.createElement('canvas');
    canvas.width = videoElement.videoWidth;
    canvas.height = videoElement.videoHeight;
    const ctx = canvas.getContext('2d');
    ctx.drawImage(videoElement, 0, 0);
    const photoUrl = canvas.toDataURL('image/jpeg');
    photos = [{ url: photoUrl, timestamp: new Date().toLocaleString() }, ...photos];
  }
</script>

<div class="camera-app">
  <div class="viewfinder">
    <video
      bind:this={videoElement}
      autoplay
      playsinline
      muted
    ></video>
    
    <div class="camera-controls">
      <button class="control-btn switch" on:click={switchCamera}>🔄</button>
      <button class="control-btn capture" on:click={takePhoto}>📸</button>
    </div>
  </div>

  {#if photos.length > 0}
    <div class="photo-gallery">
      {#each photos as photo}
        <div class="photo-item">
          <img src={photo.url} alt="Captured photo" />
          <span class="timestamp">{photo.timestamp}</span>
        </div>
      {/each}
    </div>
  {/if}
</div>

<style>
  .camera-app {
    height: 100%;
    display: flex;
    flex-direction: column;
    background: #000;
  }

  .viewfinder {
    position: relative;
    flex: 1;
    background: #000;
    overflow: hidden;
  }

  video {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .camera-controls {
    position: absolute;
    bottom: 20px;
    left: 0;
    right: 0;
    display: flex;
    justify-content: center;
    gap: 20px;
    padding: 20px;
  }

  .control-btn {
    background: rgba(255, 255, 255, 0.2);
    border: none;
    border-radius: 50%;
    width: 60px;
    height: 60px;
    font-size: 24px;
    cursor: pointer;
    transition: background-color 0.2s;
    backdrop-filter: blur(10px);
  }

  .control-btn:hover {
    background: rgba(255, 255, 255, 0.3);
  }

  .capture {
    width: 70px;
    height: 70px;
    font-size: 30px;
  }

  .photo-gallery {
    height: 100px;
    display: flex;
    gap: 10px;
    padding: 10px;
    background: #1a1a1a;
    overflow-x: auto;
  }

  .photo-item {
    position: relative;
    height: 80px;
    min-width: 80px;
    border-radius: 8px;
    overflow: hidden;
  }

  .photo-item img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .timestamp {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: rgba(0, 0, 0, 0.7);
    color: white;
    font-size: 8px;
    padding: 2px 4px;
    text-align: center;
  }
</style>
