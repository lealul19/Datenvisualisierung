<script>
    import { scaleLinear, scaleBand } from 'd3-scale';
    import { fade, fly } from 'svelte/transition';
  
    let { data = [] } = $props();
  
    const width = 500;
  
    // 🔥 DYNAMISCHE HÖHE (WICHTIG!)
    const height = data.length * 60 + 60;
  
    const margin = {
      top: 20,
      right: 30,
      bottom: 20,
      left: 140
    };
  
    const innerWidth = width - margin.left - margin.right;
    const innerHeight = height - margin.top - margin.bottom;
  
    const yScale = scaleBand()
      .domain(data.map(d => d.country))
      .range([0, innerHeight])
      .padding(0.5);
  
    const xScale = scaleLinear()
      .domain([0, 86])
      .range([0, innerWidth]);
  </script>
  
  <div class="lollipop-shell">
  
    {#if data.length > 0}
      <svg viewBox={`0 0 ${width} ${height}`} class="lollipop-svg">
  
        <g transform={`translate(${margin.left}, ${margin.top})`}>
  
          {#each data as d, i (d.country)}
            <g in:fly={{ x: 20, duration: 300 + i * 40 }} out:fade>
  
              <!-- Linie -->
              <line
                x1="0"
                x2={xScale(d.life)}
                y1={yScale(d.country) + yScale.bandwidth()/2}
                y2={yScale(d.country) + yScale.bandwidth()/2}
                stroke="#64748b"
                stroke-width="3"
              />
  
              <!-- Punkt -->
              <circle
                cx={xScale(d.life)}
                cy={yScale(d.country) + yScale.bandwidth()/2}
                r="8"
                fill="#3b82f6"
              />
  
              <!-- Name -->
              <text
                x="-10"
                y={yScale(d.country) + yScale.bandwidth()/2}
                text-anchor="end"
                alignment-baseline="middle"
                fill="#cbd5e1"
              >
                {d.country}
              </text>
  
              <!-- Wert -->
              <text
                x={xScale(d.life) + 15}
                y={yScale(d.country) + yScale.bandwidth()/2}
                alignment-baseline="middle"
                fill="#7dd3fc"
                font-weight="bold"
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