<script lang="ts">
  import ErrorPanel from "$lib/components/ErrorPanel.svelte";
  import MediaDeviceList from "$lib/components/MediaDeviceList.svelte";
  import VideoStreamObtainer from "$lib/components/VideoStreamObtainer.svelte";

  let canvas: HTMLCanvasElement;
  let video: HTMLVideoElement;
  let error = $state<unknown>();

  let width = $state<number | undefined>();
  let height = $state<number | undefined>();

  function captureScreenshot() {
    const ctx = canvas.getContext("2d");
    ctx?.drawImage(video, 0, 0);
  }
</script>

<svelte:head>
  <title>Video capture test</title>
</svelte:head>

<!-- svelte-ignore a11y_media_has_caption -->
<video bind:this={video} {width} {height}></video>
<VideoStreamObtainer
  onstream={(stream) => {
    video.srcObject = stream;
    video
      .play()
      .then(() => {
        width = video.videoWidth;
        height = video.videoHeight;
        error = undefined;
      })
      .catch((e) => {
        width = undefined;
        height = undefined;
        error = e;
      });
  }}
/>

{#if width && height}
  <p>
    <button type="button" onclick={captureScreenshot}>
      Capture a screenshot
    </button>
    Video stream's size: {width}&times;{height}
  </p>
{/if}
<ErrorPanel {error} />
<MediaDeviceList />

<canvas bind:this={canvas} {width} {height}></canvas>

<style>
  video {
    display: none;
  }
</style>
