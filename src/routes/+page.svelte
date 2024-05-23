<script lang="ts">
  let video: HTMLVideoElement;
  let canvas: HTMLCanvasElement;
  let error: unknown = undefined;

  let videoStreamPending = false;
  let width: number | undefined;
  let height: number | undefined;

  async function getVideoStream() {
    videoStreamPending = true;
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
      videoStreamPending = false;
      width = video.videoWidth;
      height = video.videoHeight;
    } catch (e) {
      console.log(e);
      error = e;
    }
  }

  function captureScreenshot() {
    const ctx = canvas.getContext("2d");
    ctx?.drawImage(video, 0, 0);
  }
</script>

<svelte:head>
  <title>Video capture test</title>
</svelte:head>

<!-- svelte-ignore a11y-media-has-caption -->
<video bind:this={video} {width} {height} />

<p>
  {#if width || height}
    <button type="button" on:click={captureScreenshot}>Take a screenshot</button
    >
    Video stream obtained, size == {width}&times;{height}
  {:else if !videoStreamPending}
    <button type="button" on:click={getVideoStream}>Get a video stream</button>
  {:else}
    Getting a video stream...
  {/if}
</p>

{#if error != null}
  <pre>
	{JSON.stringify(error, undefined, 2)}
</pre>
{/if}
<canvas bind:this={canvas} {width} {height} />

<style>
  video {
    display: none;
  }
  pre {
    color: red;
  }
</style>
