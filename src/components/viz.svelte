<script>
  import * as d3 from 'd3';
  import { onMount } from "svelte";
  import Slider from './Slider.svelte';
    import { readonly } from 'svelte/store';
  
  const width = 1200;
  const height = 800;
  const marginTop = 50;
  const marginRight = 50;
  const marginBottom = 50;
  const marginLeft = 45;

  export let data = [];
  let svg;
  let gx;
  let gy;
  let legend;
  let selectedYear = 1986;

  // goal: one bar per country, height = NO_CASES
  // 0. filter out data only for 2000
  // 1. create a <g>, write a #each loop to draw one rectangle per bar in html
  // 2. position bars on x-axis
  // 3. position bars on y-axis
  // 4. add axes

  // STEP ZERO: FILTER OUT DATA FOR SELECTED YEAR
  $: filteredData = data.filter(function(d) {
    return d.Reference_Date == selectedYear;
  })

  // DEFINE COLOR FUNCTION
  $: color = d3
    .scaleOrdinal(d3.schemeDark2);

  // DEFINE X FUNCTION
  $: x = d3
    .scaleBand()
    .domain(Array.from(new Set(filteredData.map(d => d.Country))))
    .range([marginLeft, width - marginRight])
    .padding(0.1);

  // DEFINE Y FUNCTION
  $: y = d3
    .scaleLinear()
    .domain([0, d3.max(filteredData, (d) => d.cases_normalized_percentage)])
    .nice()
    .range([height - marginBottom - marginTop, 0]);

  //static x-axis
  $: d3.select(gx)
    .attr("transform", `translate(0, ${height - marginBottom})`)
    .call(d3.axisBottom(x)
          .tickSizeOuter(0)
          .ticks(Array.from(new Set(filteredData.map(d => d.Country)))))
    .selectAll("text")
    .attr("transform", "rotate(-45) translate(0, 70)");
          
  //static y-axis
  $: d3.select(gy)
    .attr("transform", `translate(${marginLeft}, ${marginBottom})`)
    .call(d3.axisLeft(y));

  //static legend
  $: {
  const legendEntries = d3.select(legend)
    .selectAll(".legend-entry")
    .data(Array.from(new Set(filteredData.map(d => d.Region))))
    .enter().append("g")
    .attr("class", "legend-entry")
    .attr("transform", (d, i) => `translate(0, ${i * 20})`);

  legendEntries.append("rect")
    .attr("x", width - marginRight - 150)
    .attr("width", 10)
    .attr("height", 10)
    .attr("fill", color);

  legendEntries.append("text")
    .attr("x", width - marginRight - 135)
    .attr("y", 10)
    .attr("dy", "0.35em")
    .text(d => d);
  }

  // creating axes
  onMount(() => {
    svg = d3.select(".bar.plot svg")
    .attr("width", width)
    .attr("height", height);

    // add x-axis label
    svg.append("text")
      .attr("class", "x-axis-label")
      .attr("transform", `translate(${width / 2}, ${height - marginBottom / 2 + 100})`)
      .style("text-anchor", "middle")
      .text("Countries");

    // add y-axis label
    svg.append("text")
      .attr("class", "y-axis-label")
      .attr("transform", `rotate(-90)`)
      .attr("x", -height / 2 )
      .attr("y", marginLeft / 2 - 30)
      .style("text-anchor", "middle")
      .text("Number of AIDS Cases / Country Population");

    // create legend
    const legendEntries = svg.selectAll(".legend-entry")
      .data(Array.from(new Set(filteredData.map(d => d.Region))))
      .enter().append("g")
      .attr("class", "legend-entry")
      .attr("transform", (d, i) => `translate(1000, ${i * 20})`);

    legendEntries.append("rect")
      .attr("x", 0)
      .attr("width", 10)
      .attr("height", 10)
      .attr("fill", color);

    legendEntries.append("text")
      .attr("x", 15)
      .attr("y", 10)
      .attr("dy", "0.35em")
      .text(d => d);

    });



</script>

<!-- MAKE THE BAR PLOT -->
<div class = "bar plot">
<svg
  bind:this={svg}
  {width}
  {height}
  viewBox="0 0 {width} {height}"
  style="max-width: 100%; height: auto; font: 12px Georgia;"
  overflow = "visible">

<!-- x-axis -->
<g 
  bind:this={gx} 
  transform="translate(0, ${height - marginBottom})">
</g>

<!-- y-axis -->
<g 
  bind:this={gy} 
  transform="translate({marginLeft},0)">
</g>

<!-- legend -->
<g 
  bind:this={legend}>
</g>

<!-- slider -->
<input type="range" name="mySlider" id=mySlider min="1982" max="2016" value="10">

<!-- STEP 1: EACH LOOP FOR THE BARS -->
<g stroke="#000" stroke-opacity="0.2">
  {#each filteredData as d, i}

    <!-- Emojis -->
    <text
      x={x(d.Country) + x.bandwidth() / 2}
      y={y(d.cases_normalized_percentage) - 10}
      text-anchor="middle"
      font-size="20"
    >
      <!-- Add the emoji corresponding to the country -->
      {d.flag}
    </text>

    <rect
      key={i}
      x={x(d.Country)}
      y = {y(d.cases_normalized_percentage)}
      fill={color(d.Region)}
      width= {x.bandwidth()}
      height= {height - y(d.cases_normalized_percentage) - marginBottom}
    />
  {/each}
</g>
</svg>
</div>  


<div svg class = 'bar plot'
  bind:this={svg}
  {width}
  {height}
  viewBox= "0 0 {width} {height}"
  style= "max-width: 100%; height: auto;">
</div>

<div class = 'slider'>
<Slider bind:selectedYear>
    x={width / 2}
    y={height}
</Slider>
</div>

<style>
  .slider {
    transform: translate(0, 90%);
  }
</style>