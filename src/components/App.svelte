<script>
  import * as d3 from 'd3';
  import Viz from './viz.svelte';
  import { onMount } from "svelte";
    import Slider from './Slider.svelte';

  let data = [];

  onMount(
    async() => {
      const res = await fetch('aids_cases_by_region.csv')
      const csv = await res.text();
      data = d3.csvParse(csv, d3.autoType)
    }
  )

  $: console.log(data);
</script>

<main>
  <h1>🎗️ Tracking AIDS Around the Globe 🎗️ </h1>
  <h2> Journey of Hope: Mapping the Global Fight Against AIDS Through Dynamic Bar Charts</h2>
  <Viz {data} />
  <div class = 'write-up'>
    <h3> Design Decisions</h3>
    <p class = 'body-text'>
      For our visualization, we first decided to use a treemap to depict our data, but we finalized our visualization as 
      a bar chart. A treemap utilizes hierarchical data in order to graph leaf nodes, which seemed too extravagant for our purposes, 
      as we are listing the cases of AIDs by country. A bar plot matched our purposes to a tee, as it accurately displays the relative 
      frequencies of multiple categorical variables to the viewer. 

      For our encodings, we decided to encode the year of each reported set onto a slider in order to see how the number of cases 
      reported fluctuated per year. Additionally, we chose to encode each individual for each year as a separate bar on the bar chart. 
      Finally, we chose to encode the region where each country resides as the color of the bars, as it gives the viewer an easy way to differentiate between the relative regions of the world in regards to the number of cases reported.
    </p>
    <h3> Development Process</h3>
    <p class = 'body-text'>
      Firstly, we had to perform data cleaning on our data in order to extract the information that we needed specifically for our 
      visualization. This was accomplished by downloading data from the U.S. Bureau and merging it with another data set that 
      described the total population of each country. Next, we imported this data into our data visualization, which proved to be 
      challenging. Overall, the exploratory data analysis of our visualization took about four hours to complete.
      
      Secondly, we began the implementation of our visualization through code. We used a multitude of resources and gained 
      inspiration from the various data visualizations we viewed during lecture. Our visualization consists of three main 
      components: the bar graph, the legend, and the slider bar. The bar graph was relatively easy to implement, as we were able to 
      utilize the code from lecture 7 as a template for our bar chart. The basis for the bar chart was completed by Heidi, and Paige 
      finalized the display and functionality of the chart. Next, we implemented the legend of the chart, which was done by Heidi. 
      However, in order to make the axes and legend static within the visualization, Paige implemented each component as reactive 
      variables. Finally, the slider of the visualization was implemented. This was done by adding a new slider component to the 
      visualization.
    </p>
  </div>

</main>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Nunito:wght@300;400;700&display=swap');

  :root {
    --color-bg: #FF0000;
    --color-outline: #c2c2c2;
    --color-shadow: hsl(0, 0%, 0%, 0.1);
    --color-text: #000000;
    --color-bg-1: hsla(0, 0%, 0%, 0.2);
    --color-shadow-1: hsl(0, 0%, 96%);
  }

  *,
  *::before,
  *::after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  main {
    text-align: center;
    font-family: 'Nunito', sans-serif;
    font-weight: 300;
    line-height: 2;
    font-size: 24px;
    color: var(--color-text);
  }

  h1 {
    font-size: 2em;
    font-weight: 300;
    line-height: 2;
    background-color: #FF6961;
  }

  h3 {
    font-size: 2em;
    font-weight: 500;
  }

  .write-up {
    transform:translate(0,50%)
  }

  .body-text {
    font-size: 1em;
    font-weight: 300;
  }
</style>