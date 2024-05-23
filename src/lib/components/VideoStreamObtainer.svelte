<script lang="ts">
  import ErrorPanel from "$lib/components/ErrorPanel.svelte";

  let {
    onstream,
  }: {
    onstream?: (stream: MediaStream) => void;
  } = $props();

  type ConstraintSpecifierEnum = "none" | "exact" | "ideal";

  let videoStreamPending = $state(false);
  let error = $state<unknown>(undefined);
  let facingMode = $state<VideoFacingModeEnum>("user");
  let facingModeSpecifier = $state<ConstraintSpecifierEnum>("none");

  function constraint<T>(
    value: T,
    specifier: ConstraintSpecifierEnum
  ): T | { exact: T } | { ideal: T } {
    if (specifier === "exact") {
      return { exact: value };
    }
    if (specifier === "ideal") {
      return { ideal: value };
    }
    return value;
  }

  async function getVideoStream() {
    if (videoStreamPending) {
      return;
    }
    videoStreamPending = true;
    try {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: {
          facingMode: constraint(facingMode, facingModeSpecifier),
          width: { ideal: 4096 },
          height: { ideal: 2160 },
        },
      });
      onstream?.(stream);
      error = undefined;
    } catch (e) {
      console.log(e);
      error = e;
    } finally {
      videoStreamPending = false;
    }
  }
</script>

<fieldset>
  <form
    onsubmit={(e) => {
      e.preventDefault();
      getVideoStream();
    }}
  >
    <legend>Obtain media stream</legend>
    <p>
      <label>
        Facing Mode
        <select name="facingMode" bind:value={facingMode}>
          <option value="user">user</option>
          <option value="environment">environment</option>
        </select>
      </label>
    </p>
    <p>
      <label>
        Facing Mode Specifier
        <select name="facingMode" bind:value={facingModeSpecifier}>
          <option value="none">None</option>
          <option value="exact">Exact</option>
          <option value="ideal">Ideal</option>
        </select>
      </label>
    </p>

    <p>
      <button type="submit" disabled={videoStreamPending}>
        Get a video stream
      </button>
    </p>

    {#if videoStreamPending}
      <p>Getting a video stream...</p>
    {/if}

    <ErrorPanel {error} />
  </form>
</fieldset>
