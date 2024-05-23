<script lang="ts">
  import { onMount } from "svelte";

  let video: HTMLVideoElement;
  let canvas: HTMLCanvasElement;
  let error: unknown = undefined;

  onMount(async () => {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: {
          facingMode: "user",
          width: { ideal: 4096 },
          height: { ideal: 2160 },
        },
      });
      video.srcObject = stream;
      await video.play();
      video.height = video.videoHeight;
      video.width = video.videoWidth;
      canvas.height = video.videoHeight;
      canvas.width = video.videoWidth;
    } catch (e) {
      console.log(e);
      error = e;
    }
  });

  function captureScreenshot() {
    const ctx = canvas.getContext("2d");
    ctx?.drawImage(video, 0, 0);
  }
</script>

<svelte:head>
  <title>Video capture test</title>
</svelte:head>

<!-- svelte-ignore a11y-media-has-caption -->
<video bind:this={video} />
<p>
  <button type="button" on:click={captureScreenshot}>Take screenshot</button>
</p>
{#if error != null}
  <pre>
	{JSON.stringify(error, undefined, 2)}
</pre>
{/if}
<canvas bind:this={canvas} />

<style>
  video {
    display: none;
  }
  pre {
    color: red;
  }
</style>
