<script lang="ts">
  let { error }: { error: unknown } = $props();

  let formatted = $derived.by(() => {
    if (error instanceof Error) {
      return `${error.name} ${error.message}\n${error.stack}`;
    }
    if (typeof error === "string") {
      return error;
    }
    return JSON.stringify(error, undefined, 2);
  });
</script>

{#if error != null}
  <pre>{formatted}</pre>
{/if}

<style>
  pre {
    color: red;
  }
</style>
