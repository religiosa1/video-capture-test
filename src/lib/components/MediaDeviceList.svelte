<script lang="ts">
  let mediaDevices = $state<Promise<MediaDeviceInfo[]> | undefined>();

  async function getDevices() {
    const mediaDevices = await navigator.mediaDevices.enumerateDevices();
    return mediaDevices.filter((d) => d.kind === "videoinput");
  }
</script>

{#if mediaDevices != null}
  {#await mediaDevices}
    <p>Loading media devices list</p>
  {:then devices}
    <ul>
      {#each devices as device}
        <li>
          {device.deviceId}
          {device.groupId}
          {device.kind}
          {device.label}
        </li>
      {/each}
    </ul>
  {/await}
{/if}

<button type="button" onclick={() => (mediaDevices = getDevices())}>
  Get a list of media devices
</button>
