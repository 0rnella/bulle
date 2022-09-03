<script lang="ts">
  // Source code from blog post: https://spin.atomicobject.com/2022/01/17/text-recognition-tesseract-js/
  import { createWorker } from "tesseract.js";

  let video, stream, worker, ctx, canvas;
  let running = false;
  let output = "";

  const initWorker = async () => {
    worker = createWorker({
      logger: (m) => console.log(m),
    });
    await worker.load();
    await worker.loadLanguage("eng");
    await worker.initialize("eng");
  };

  const capture = async () => {
    ctx = canvas.getContext("2d");
    ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
    const img = canvas.toDataURL("image/png");
    const {
      data: { text },
    } = await worker.recognize(img);
    output = text;
  };

  const start = async () => {
    try {
      stream = await navigator.mediaDevices.getUserMedia({
        video: true,
      });
      video.srcObject = stream;
      video.play();
      running = true;

      initWorker();
    } catch (error) {
      /* Do you have a webcam? */
      console.log(error);
    }
  };

  const stop = async () => {
    try {
      stream.getTracks().forEach(function (track) {
        if (track.readyState == "live") {
          track.stop();
        }
      });
      running = false;
    } catch (err) {
      console.log(err);
    }
  };
</script>

<main>
  <h1>welcome, scan your stuff</h1>
  <button on:click={start} class={running && "hidden"}>Start filming</button>
  <div class={!running && "hidden"}>
    <p>
      <button on:click={stop}>Stop filming</button>
    </p>
    <button on:click={capture}>Capture</button>
    <p>{output}</p>
    <!-- svelte-ignore a11y-media-has-caption -->
    <video bind:this={video} />

    <canvas bind:this={canvas} />
  </div>
</main>

<style>
  .hidden {
    display: none;
  }
</style>
