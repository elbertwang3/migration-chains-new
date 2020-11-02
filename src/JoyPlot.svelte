<script>
  import { beforeUpdate } from "svelte";
  import { select } from "d3-selection";
  import { scaleLinear, scaleOrdinal } from "d3-scale";
  import { schemeBrBG } from "d3-scale-chromatic";
  import { max, extent } from "d3-array";
  import { axisBottom } from "d3-axis";
  import { format } from "d3-format";
  import { area, curveCardinal } from "d3-shape";
  import Joy from "./Joy.svelte";

  export let width = 200;
  export let height = 200;
  export let data;
  const overlap = 0.5;
  console.log(data);

  const margin = { top: 25, right: 0, bottom: 20, left: 0 };
  $: chartWidth = width - margin.left - margin.right;
  $: chartHeight = height - margin.top - margin.bottom;
  $: plotHeight = ((1 + overlap) * chartHeight) / data.length;

  const formatIncome = format("$,");

  // let colorScale = scaleQuantile()
  //   .domain(Object.values(census))
  //   .range(schemeBrBG[11].slice(3, 8));

  // console.log(extent(Object.values(census)));
  $: xValues = data.map((d) => d.values.map((d) => d.rv)).flat();
  $: xScale = scaleLinear()
    .domain(extent(xValues))
    .nice()
    .range([0, chartWidth]);
  $: xAxis = axisBottom(xScale).tickFormat(formatIncome);
  let xAxisG;
  // $: yValues = data.map((d) => d.values.map((d) => d.p)).flat();
  // console.log(max(yValues));
  // $: yScale = scaleLinear()
  //   .domain([0, max(yValues)])
  //   .nice()
  //   .range([plotHeight, 0]);

  $: areaGenerator = area()
    .curve(curveCardinal)
    .x((d) => xScale(d.rv))
    .y0((d) => yScale(0))
    .y1((d) => yScale(d.p));

  // const colorScale = scaleSequential(interpolateBrBG).domain([30000, 100000]);
  const colorScale = scaleOrdinal()
    .domain(data.map((d) => d.key).reverse())
    .range(schemeBrBG[data.length]);

  beforeUpdate(() => {
    console.log("after update");
    select(xAxisG).call(xAxis);
  });
</script>

<style>
  /* your styles go here */
  svg {
    overflow: visible;
  }
</style>

<svg {width} {height}>
  <defs />
  <g transform={`translate(${margin.left}, ${margin.top})`}>
    <g class="g-axis">
      <g
        class="g-x-axis"
        bind:this={xAxisG}
        transform={`translate(${0}, ${chartHeight})`} />
    </g>
    <g class="g-areas">
      {#each data as round, i}
        <g
          class={`g-area-${round.key}`}
          transform={`translate(0, ${(chartHeight * (i - overlap)) / data.length})`}>
          <text class="round-label" y={plotHeight - 3}>{round.key}</text>
          <Joy
            data={round.values}
            {xScale}
            {plotHeight}
            fill={colorScale(round.key)} />
          <!-- <path
            class="area-path"
            d={areaGenerator(round.values)}
            fill="none"
            stroke="red"
            fill-opacity={0.1} /> -->
        </g>
      {/each}
    </g>
  </g>
</svg>
