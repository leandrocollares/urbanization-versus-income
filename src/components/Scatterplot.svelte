<script>
  import * as d3 from "d3";
  import Chart from "./Chart/Chart.svelte";
  import XAxis from "./Chart/XAxis.svelte";
  import YAxis from "./Chart/YAxis.svelte";
  import Tooltip from "./Chart/Tooltip.svelte";
  import Crosshair from "./Chart/Crosshair.svelte";

  export let data;

  const textAccessor = (d) => d.country;
  const xAccessor = (d) => +d.gdpPerCapita;
  const yAccessor = (d) => +d.urbanPopulation;

  const formatIncome = d3.format(".2s");
  const formatXLabel = d3.format(",.0f");
  const formatYLabel = d3.format(".1f");

  let width = 100;
  let height = 100;

  let hoveredCircle = null;
  let xAccessorScaled;
  let yAccessorScaled;

  const margins = {
    marginTop: 40,
    marginRight: 70,
    marginBottom: 65,
    marginLeft: 70,
  };

  $: dms = {
    width,
    height,
    ...margins,
    boundedHeight: Math.max(
      height - margins.marginTop - margins.marginBottom,
      0
    ),
    boundedWidth: Math.max(width - margins.marginLeft - margins.marginRight, 0),
  };

  $: xScale = d3
    .scaleSymlog()
    .domain([0, d3.max(data, xAccessor)])
    .rangeRound([0, dms.boundedWidth])
    .constant(5000)
    .nice();

  $: yScale = d3
    .scaleLinear()
    .domain([0, d3.max(data, yAccessor)])
    .range([dms.boundedHeight, 0])
    .nice();

  $: xAccessorScaled = (d) => xScale(xAccessor(d));
  $: yAccessorScaled = (d) => yScale(yAccessor(d));

  const handleMouseEnter = (d) => {
    hoveredCircle = d;
  };

  const handleMouseLeave = () => {
    hoveredCircle = null;
  };
</script>

<div
  class="scatterplot__placeholder"
  bind:clientWidth={width}
  bind:clientHeight={height}
>
  <Chart dimensions={dms}>
    {#each data as d}
      <circle
        r="6"
        cx={xAccessorScaled(d)}
        cy={yAccessorScaled(d)}
        on:mouseenter={handleMouseEnter(d)}
        on:mouseleave={handleMouseLeave}
      />
    {/each}
    <XAxis
      scale={xScale}
      label="GDP per capita (constant 2010 USD)"
      formatTick={formatIncome}
    />
    <YAxis scale={yScale} label="Urban population (%)" />
    {#if hoveredCircle}
      <Crosshair
        xAccessorScaled={xAccessorScaled(hoveredCircle)}
        yAccessorScaled={yAccessorScaled(hoveredCircle)}
        xLabel={formatXLabel(xAccessor(hoveredCircle))}
        yLabel={formatYLabel(yAccessor(hoveredCircle))}
        boundedHeight={dms.boundedHeight}
      />
    {/if}
  </Chart>
  {#if hoveredCircle}
    <Tooltip
      xAccessorScaled={xAccessorScaled(hoveredCircle)}
      yAccessorScaled={yAccessorScaled(hoveredCircle)}
      tooltipText={textAccessor(hoveredCircle)}
      marginLeft={margins.marginLeft}
      marginTop={margins.marginTop}
    />
  {/if}
</div>

<style>
  .scatterplot__placeholder {
    position: relative;
    width: 100%;
    height: 320px;
    max-width: 700px;
    margin: 0 40px;
  }

  circle {
    fill: #4427ca;
    opacity: 0.6;
  }

  circle:hover {
    fill: #dc267f;
    opacity: 1;
  }
</style>
