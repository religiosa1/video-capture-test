<script lang="ts">
  let { blob }: { blob: Blob } = $props();

  let url = $derived(URL.createObjectURL(blob));

  let naturalWidth = $state<number | undefined>();
  let naturalHeight = $state<number | undefined>();
</script>

<p>
  Screenshot size: {naturalWidth}&times;{naturalHeight}<br />
  <img src={url} alt="screenshot" bind:naturalWidth bind:naturalHeight />
</p>
