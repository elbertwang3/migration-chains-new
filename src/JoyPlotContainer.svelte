<script>
  import Select from "svelte-select";
  import JoyPlot from "./Joyplot.svelte";
  import metros from "./data/metros.json";
  import hhinc from "./data/hhinc.json";
  import ipc from "./data/ipc.json";

  const data = {
    hhinc: hhinc,
    ipc: ipc,
  };

  export let value;
  let joyplotWidth;
  let joyplotHeight;

  console.log(value);

  let selectedMetro = {
    label: "Seattle",
    value: "seattle",
  };
</script>

<style>
  .dropdown {
    max-width: 300px;
    margin: auto;
  }
  .dropdown-title {
    margin-bottom: 0.5rem;
    font-weight: 600;
  }

  .joyplot {
    max-width: 600px;
    height: 500px;
    margin: 0 auto 2rem auto;
  }

  .joyplot-title {
    font-size: 24px;
    font-weight: bold;
    text-align: center;
    margin: 0 0 1rem 0;
  }
</style>

<!-- <svg {width} {height}>
  <g transform={`translate(${margin.left},${margin.top})`}>
    <g class="g-axes">
      <g
        class="axis x-axis"
        bind:this={xAxisG}
        transform={`translate(0, ${chartHeight})`} />
      <g class="axis y-axis" bind:this={yAxisG}>
      </g>
    </g>
    <g class="g-lines" />
  </g>
</svg> -->

<div class="joyplot-title">{value.title}</div>
<div class="dropdown">
  <div class="dropdown-title">Select a metro area</div>
  <Select items={metros} bind:selectedValue={selectedMetro} />
</div>
<div
  class="joyplot"
  bind:clientWidth={joyplotWidth}
  bind:clientHeight={joyplotHeight}>
  <JoyPlot
    width={joyplotWidth}
    height={joyplotHeight}
    data={data[value.type][selectedMetro.value]} />
</div>
