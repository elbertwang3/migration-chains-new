<script>
  import { area, curveCardinal } from "d3-shape";
  import { scaleLinear } from "d3-scale";
  import { max } from "d3-array";

  export let data;
  export let xScale;
  export let plotHeight;
  export let fill;
  console.log(data);

  $: yScale = scaleLinear()
    .domain([0, max(data, (d) => d.p)])
    .nice()
    .range([plotHeight, 0]);

  $: areaGenerator = area()
    .curve(curveCardinal)
    .x((d) => xScale(d.rv))
    .y0((d) => yScale(0))
    .y1((d) => yScale(d.p));
</script>

<path
  class="area-path"
  d={areaGenerator(data)}
  {fill}
  stroke="white"
  fill-opacity={0.75} />
