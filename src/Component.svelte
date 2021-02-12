<script>
  export let endpoint;

  let loading = true;
  let data;
  let containerWidth;
  let currentBreakpoint = "sm";

  $: if (endpoint) {
    fetch(endpoint)
      .then((res) => res.json())
      .then((json) => {
        data = json;
      })
      .catch((error) => console.log("Error:", error.message));
  }

  $: loading = !data;

  $: {
    if (containerWidth > 700) {
      currentBreakpoint = "lg";
    } else if (containerWidth > 400) {
      currentBreakpoint = "md";
    } else {
      currentBreakpoint = "sm";
    }
  }
</script>

<svelte:options tag="interactive-component" />

<div
  bind:clientWidth={containerWidth}
  class="container breakpoint-{currentBreakpoint}"
>
  {#if loading}
    <p class="loading">... loading ...</p>
  {:else}
    <pre class="data">{JSON.stringify(data, null, 4)}</pre>
  {/if}
</div>

<style lang="scss">
  .container {
    width: 100%;
    .data {
      width: 100%;
      overflow: scroll;
      background: lightgray;
    }

    &.breakpoint-lg .data {
      height: 300px;
    }
    &.breakpoint-md .data {
      height: 400px;
    }
    &.breakpoint-sm .data {
      height: 500px;
    }
  }
</style>
