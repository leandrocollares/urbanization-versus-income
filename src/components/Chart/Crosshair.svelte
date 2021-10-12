<script>
  export let xAccessorScaled;
  export let yAccessorScaled;
  export let xLabel;
  export let yLabel;
  export let boundedHeight;
</script>

<g transform={`translate(${xAccessorScaled} 0)`}>
  <text
    class="crosshair__text"
    x={0}
    y={boundedHeight + 42}
    text-anchor="middle"
  >
    {xLabel}
  </text>
  <line
    class="crosshair__line"
    x1={0}
    x2={0}
    y1={yAccessorScaled}
    y2={boundedHeight + 25}
  />
</g>

<g transform={`translate(0 ${yAccessorScaled})`}>
  <text class="crosshair__text" x={-50} y={0} dy="0.34em" text-anchor="middle">
    {yLabel}
  </text>
  <line class="crosshair__line" x1={xAccessorScaled} x2={-30} y1={0} y2={0} />
</g>

<style>
  .crosshair__text {
    fill: #dc267f;
  }

  .crosshair__line {
    stroke: #dc267f;
  }
</style>
