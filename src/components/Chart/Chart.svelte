<script>
  import { setContext } from "svelte";
  import { writable } from "svelte/store";
  export let dimensions;

  let currentDimensions = writable(dimensions);

  $: {
    currentDimensions.set(dimensions);
  }

  setContext("chart", {
    dimensions: currentDimensions,
  });
</script>

<svg class="chart" width={dimensions.width} height={dimensions.height}>
  <g transform={`translate(${dimensions.marginLeft}, ${dimensions.marginTop})`}>
    <slot />
  </g>
</svg>
