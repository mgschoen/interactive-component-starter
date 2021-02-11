<script>
  export let endpoint;

  let loading = true;
  let images = [];
  let data;
  let imageBaseUrl;
  let elementWidth;
  let currentBreakpoint = "sm";

  $: if (endpoint) {
    fetch(endpoint)
      .then((res) => res.json())
      .then((json) => {
        data = json;
      })
      .catch((error) => console.log("Error:", error.message));
  }

  $: imageBaseUrl = endpoint
    ? `${new URL(endpoint).origin}/assets/image`
    : null;

  $: if (data && data.elements && imageBaseUrl) {
    images = data.elements.map((entry) => {
      let imageId;
      try {
        imageId = entry.image[0].reference._source.id;
      } catch (_) {
        return null;
      }
      return {
        url: `${imageBaseUrl}/${imageId}_tm.jpg`,
      };
    });
  }

  $: loading = !images.length;

  $: {
    if (elementWidth > 732) {
      currentBreakpoint = "lg";
    } else if (elementWidth > 432) {
      currentBreakpoint = "md";
    } else {
      currentBreakpoint = "sm";
    }
  }

  function handleMouseEnter(e) {
    e.target.style.transform = "scale(1.5)";
  }

  function handleMouseLeave(e) {
    e.target.style.transform = "";
  }
</script>

<!--- Configure SPA ---->
<svelte:options tag="interactive-component" />

<div class="container {currentBreakpoint}" bind:clientWidth={elementWidth}>
  {#if loading}
    <p class="loader">...loading</p>
  {:else}
    {#each images as image}
      <img
        src={image.url}
        alt=""
        on:mouseenter={handleMouseEnter}
        on:mouseleave={handleMouseLeave}
      />
    {/each}
  {/if}
</div>

<style lang="scss">
  .loader {
    color: red;
  }
  .container {
    padding: 16px;

    img {
      transition: transform 0.5s ease-in-out;
    }

    &.lg img {
      width: 33%;
    }
    &.md img {
      width: 50%;
    }
    &.sm img {
      width: 100%;
    }
  }
</style>
