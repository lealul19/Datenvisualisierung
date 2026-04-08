<script>
    import { scaleLinear, scaleBand } from 'd3-scale';
    import { fade, fly } from 'svelte/transition';
  
    let { data = [] } = $props();
  
    const width = 560;
  
    const minRows = 8;
    const rowHeight = 52;
    const visibleRows = Math.max(data.length, minRows);
  
    const margin = {
      top: 16,
      right: 60,
      bottom: 16,
      left: 150
    };
  
    const height = visibleRows * rowHeight + margin.top + margin.bottom;
    const innerWidth = width - margin.left - margin.right;
    const innerHeight = height - margin.top - margin.bottom;
  
    const yScale = scaleBand()
      .domain(data.map((d) => d.country))
      .range([0, innerHeight])
      .padding(0.42);
  
    const xScale = scaleLinear()
      .domain([0, 86])
      .range([0, innerWidth]);
  
    const xTicks = [0, 20, 40, 60, 80];
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
                alignment-baseline="middle"
                class="lollipop-label"
              >
                {d.country}
              </text>
  
              <text
                x={xScale(d.life) + 14}
                y={yScale(d.country) + yScale.bandwidth() / 2}
                alignment-baseline="middle"
                class="lollipop-value"
              >
                {d.life}
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