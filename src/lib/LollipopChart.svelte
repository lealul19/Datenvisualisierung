<script>
    import { scaleLinear, scaleBand } from 'd3-scale';
    import { fade, fly } from 'svelte/transition';
  
    let { data = [] } = $props();
  
    const width = 560;
  
    const margin = {
      top: 20,
      right: 55,
      bottom: 20,
      left: 150
    };
  
    const rowHeight = 52;
    const minHeight = 420;
  
    const calculatedHeight = data.length * rowHeight + margin.top + margin.bottom;
    const height = Math.max(minHeight, calculatedHeight);
  
    const innerWidth = width - margin.left - margin.right;
    const innerHeight = height - margin.top - margin.bottom;
  
    const yScale = scaleBand()
      .domain(data.map((d) => d.country))
      .range([0, innerHeight])
      .padding(0.45);
  
    const xScale = scaleLinear()
      .domain([0, 86])
      .range([0, innerWidth]);
  
    const xTicks = [0, 20, 40, 60, 80];
  
    function formatLife(value) {
      return `${value}`;
    }
  </script>
  
  <div class="lollipop-shell">
    {#if data.length > 0}
      <svg viewBox={`0 0 ${width} ${height}`} class="lollipop-svg" aria-label="Lollipop chart">
        <g transform={`translate(${margin.left}, ${margin.top})`}>
          {#each xTicks as tick}
            <line
              x1={xScale(tick)}
              x2={xScale(tick)}
              y1="0"
              y2={innerHeight}
              class="grid-line"
            />
          {/each}
  
          <line x1="0" y1={innerHeight} x2={innerWidth} y2={innerHeight} class="axis-line" />
  
          {#each xTicks as tick}
            <g transform={`translate(${xScale(tick)}, ${innerHeight})`}>
              <line y2="6" class="tick-line" />
              <text y="22" text-anchor="middle" class="tick-text">
                {tick}
              </text>
            </g>
          {/each}
  
          {#each data as d, i (d.country)}
            <g in:fly={{ x: 24, duration: 280 + i * 40 }} out:fade={{ duration: 140 }}>
              <line
                x1="0"
                x2={xScale(d.life)}
                y1={yScale(d.country) + yScale.bandwidth() / 2}
                y2={yScale(d.country) + yScale.bandwidth() / 2}
                class="lollipop-line"
              />
  
              <circle
                cx={xScale(d.life)}
                cy={yScale(d.country) + yScale.bandwidth() / 2}
                r="8"
                class="lollipop-dot"
              />
  
              <text
                x="-14"
                y={yScale(d.country) + yScale.bandwidth() / 2}
                text-anchor="end"
                dominant-baseline="middle"
                class="lollipop-label"
              >
                {d.country}
              </text>
  
              <text
                x={xScale(d.life) + 14}
                y={yScale(d.country) + yScale.bandwidth() / 2}
                dominant-baseline="middle"
                class="lollipop-value"
              >
                {formatLife(d.life)}
              </text>
            </g>
          {/each}
        </g>
      </svg>
    {:else}
      <div class="lollipop-empty">
        <h3>Keine Daten</h3>
        <p>Filter ändern</p>
      </div>
    {/if}
  </div>