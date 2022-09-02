<script lang="ts">
  let video, stream, canvas;
  let running = false;

   const start = async () => {
    try {
      stream = await navigator.mediaDevices.getUserMedia({
        video: true,
      });
      video.srcObject = stream;
      video.play();
      running = true;
      // video.onresize = () => {
      //   canvas.width = video.videoWidth;
      //   canvas.height = video.videoHeight;
      //   running = true;
      // };
    } catch (error) {
      /* Do you have a webcam? */
      console.log(error);
    }
  };

  const stop = async () => {
    try {
      stream.getTracks().forEach(function(track) {
          if (track.readyState == 'live') {
              track.stop();
          }
      });
      running = false;
    } catch (err) {
      console.log(err);
    }
  }

</script>

<main>

  <h1>welcome, scan your stuff</h1>
  <button on:click={start} class={running && "hidden"}>Start filming</button>
  <div class={!running && "hidden"}>
    <p>
      <button on:click={stop}>Stop filming</button>
    </p>
    <!-- svelte-ignore a11y-media-has-caption -->
    <video bind:this={video}/>
  
    <canvas bind:this={canvas} />
  </div>

</main>

<style>
.hidden {
  display: none;
}
</style>