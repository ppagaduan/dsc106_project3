<script>
    import { createEventDispatcher, onDestroy } from 'svelte';
  
    export let selectedYear = 1986;
    export let autoplay = false;
  
    const dispatch = createEventDispatcher();
  
    let intervalId;
  
    const handleChange = (event) => {
      selectedYear = parseInt(event.target.value);
      dispatch('change', selectedYear);
    };
  
    const handleAutoplay = () => {
      autoplay = !autoplay;
      if (autoplay) {
        intervalId = setInterval(() => {
            // MAXIMUM YEAR
          if (selectedYear < 2016) {
            selectedYear++;
          } else { // MINIMUM YEAR
            selectedYear = 1986;
          }
          // this controls the delay
          dispatch('change', selectedYear);
        }, 500);
      } else {
        clearInterval(intervalId);
      }
    };
  </script>
  
  <label for="year">Choose a year:</label> <button on:click={handleAutoplay}>{autoplay ? 'Pause' : 'Autoplay'}</button>
  <div> </div>
  <span>{selectedYear}</span>
  <input type="range" id="year" min="1986" max="2016" bind:value={selectedYear} on:input={handleChange}>
  <div></div>

  
