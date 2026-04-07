<script>
    import { scaleBand, scaleLinear, scaleOrdinal } from 'd3-scale';
    import { max } from 'd3-array';
    import { fade, fly } from 'svelte/transition';
  
    let { data = [], metric = 'gdp' } = $props();
  
    const width = 1200;
    const height = 420;
  
    const margin = {
      top: 24,
      right: 24,
      bottom: 70,
      left: 70
    };
  
    const innerWidth = width - margin.left - margin.right;
    const innerHeight = height - margin.top - margin.bottom;
  
    const sortedData = [...data]
      .sort((a, b) => b[metric] - a[metric])
      .slice(0, 8);
  
    const xScale = scaleBand()
      .domain(sortedData.map((d) => d.country))
      .range([0, innerWidth])
      .padding(0.28);
  
    const yScale = scaleLinear()
      .domain([0, max(sortedData, (d) => d[metric]) || 0])
      .nice()
      .range([innerHeight, 0]);
  
    const colorScale = scaleOrdinal()
      .domain(sortedData.map((d) => d.country))
      .range([
        '#60a5fa',
        '#38bdf8',
        '#22d3ee',
        '#2dd4bf',
        '#818cf8',
        '#a78bfa',
        '#f472b6',
        '#fb7185'
      ]);
  
    const yTicks = yScale.ticks(5);
  
    function formatValue(value) {
      if (metric === 'gdp') return Math.round(value / 1000) + 'k';
      if (metric === 'population') return Math.round(value) + ' Mio.';
      return value;
    }
  </script>
  
  <div class="column-shell">
    <div class="column-head">
      <div>
        <p class="mini-chart-label">Chart 04</p>
        <h3>{metric === 'gdp' ? 'Top GDP Countries' : 'Top Population Countries'}</h3>
      </div>
      <p class="mini-chart-copy">
        {metric === 'gdp' ? 'Vergleich des BIP pro Kopf' : 'Vergleich der Bevölkerung'}
      </p>
    </div>
  
    <div class="column-wrap">
      <svg viewBox={`0 0 ${width} ${height}`} class="column-svg" aria-label="Column chart">
        <g transform={`translate(${margin.left}, ${margin.top})`}>
          {#each yTicks as tick}
            <line
              x1="0"
              x2={innerWidth}
              y1={yScale(tick)}
              y2={yScale(tick)}
              class="grid-line"
            />
          {/each}
  
          <line x1="0" y1={innerHeight} x2={innerWidth} y2={innerHeight} class="axis-line" />
          <line x1="0" y1="0" x2="0" y2={innerHeight} class="axis-line" />
  
          {#each yTicks as tick}
            <g transform={`translate(0, ${yScale(tick)})`}>
              <line x2="-6" class="tick-line" />
              <text x="-12" dy="0.35em" text-anchor="end" class="tick-text">
                {formatValue(tick)}
              </text>
            </g>
          {/each}
  
          {#each sortedData as d, i (d.country)}
            <g in:fly={{ y: 18, duration: 280 + i * 35 }} out:fade={{ duration: 140 }}>
              <rect
                x={xScale(d.country)}
                y={yScale(d[metric])}
                width={xScale.bandwidth()}
                height={innerHeight - yScale(d[metric])}
                rx="12"
                class="column-bar"
                fill={colorScale(d.country)}
              />
  
              <text
                x={xScale(d.country) + xScale.bandwidth() / 2}
                y={yScale(d[metric]) - 10}
                text-anchor="middle"
                class="column-value"
              >
                {formatValue(d[metric])}
              </text>
  
              <text
                x={xScale(d.country) + xScale.bandwidth() / 2}
                y={innerHeight + 24}
                text-anchor="middle"
                class="column-label"
              >
                {d.country}
              </text>
            </g>
          {/each}
        </g>
      </svg>
    </div>
  </div>