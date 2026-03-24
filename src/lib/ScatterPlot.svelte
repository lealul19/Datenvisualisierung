<script>
    import { scaleLinear, scaleSqrt, scaleOrdinal } from 'd3-scale';
    import { max } from 'd3-array';
  
    let { data = [] } = $props();
  
    const width = 980;
    const height = 560;
  
    const margin = {
      top: 30,
      right: 40,
      bottom: 75,
      left: 75
    };
  
    let hovered = $state(null);
    let tooltipX = $state(0);
    let tooltipY = $state(0);
  
    const innerWidth = width - margin.left - margin.right;
    const innerHeight = height - margin.top - margin.bottom;
  
    const continents = $derived([...new Set(data.map((d) => d.continent))]);
  
    const colorScale = $derived(
      scaleOrdinal()
        .domain(continents)
        .range(['#2563eb', '#14b8a6', '#f59e0b', '#ef4444', '#8b5cf6', '#22c55e'])
    );
  
    const xScale = $derived(
      scaleLinear()
        .domain([0, max(data, (d) => d.gdp) * 1.08])
        .range([0, innerWidth])
    );
  
    const yScale = $derived(
      scaleLinear()
        .domain([50, max(data, (d) => d.life) * 1.03])
        .range([innerHeight, 0])
    );
  
    const radiusScale = $derived(
      scaleSqrt()
        .domain([0, max(data, (d) => d.population)])
        .range([6, 24])
    );
  
    const xTicks = $derived(xScale.ticks(6));
    const yTicks = $derived(yScale.ticks(6));
  
    function showTooltip(event, d) {
      hovered = d;
      tooltipX = event.clientX;
      tooltipY = event.clientY;
    }
  
    function moveTooltip(event) {
      tooltipX = event.clientX;
      tooltipY = event.clientY;
    }
  
    function hideTooltip() {
      hovered = null;
    }
  
    function formatGDP(value) {
      return new Intl.NumberFormat('de-DE').format(value) + ' USD';
    }
  
    function formatPopulation(value) {
      return new Intl.NumberFormat('de-DE').format(value) + ' Mio.';
    }
  </script>
  
  <div class="viz-card">
    <div class="viz-top">
      <div class="legend">
        {#each continents as continent}
          <div class="legend-item">
            <span
              class="legend-dot"
              style={`background-color:${colorScale(continent)};`}
            ></span>
            <span>{continent}</span>
          </div>
        {/each}
      </div>
    </div>
  
    <div class="chart-wrap">
      <svg viewBox={`0 0 ${width} ${height}`} class="chart-svg" aria-label="Scatterplot">
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
          <line x1="0" y1="0" x2="0" y2={innerHeight} class="axis-line" />
  
          {#each xTicks as tick}
            <g transform={`translate(${xScale(tick)}, ${innerHeight})`}>
              <line y2="6" class="tick-line" />
              <text y="24" text-anchor="middle" class="tick-text">
                {Math.round(tick / 1000)}k
              </text>
            </g>
          {/each}
  
          {#each yTicks as tick}
            <g transform={`translate(0, ${yScale(tick)})`}>
              <line x2="-6" class="tick-line" />
              <text x="-12" dy="0.35em" text-anchor="end" class="tick-text">
                {tick}
              </text>
            </g>
          {/each}
  
          <text x={innerWidth / 2} y={innerHeight + 55} text-anchor="middle" class="axis-label">
            BIP pro Kopf (USD)
          </text>
  
          <text
            transform={`translate(-52, ${innerHeight / 2}) rotate(-90)`}
            text-anchor="middle"
            class="axis-label"
          >
            Lebenserwartung (Jahre)
          </text>
  
          {#each data as d}
            <circle
              cx={xScale(d.gdp)}
              cy={yScale(d.life)}
              r={radiusScale(d.population)}
              fill={colorScale(d.continent)}
              class="data-point"
              class:is-active={hovered?.country === d.country}
              onmouseenter={(event) => showTooltip(event, d)}
              onmousemove={moveTooltip}
              onmouseleave={hideTooltip}
            />
          {/each}
        </g>
      </svg>
  
      {#if hovered}
        <div
          class="tooltip"
          style={`left:${tooltipX + 18}px; top:${tooltipY - 18}px;`}
        >
          <div class="tooltip-title">{hovered.country}</div>
          <div class="tooltip-row"><span>Kontinent</span><strong>{hovered.continent}</strong></div>
          <div class="tooltip-row"><span>BIP pro Kopf</span><strong>{formatGDP(hovered.gdp)}</strong></div>
          <div class="tooltip-row"><span>Lebenserwartung</span><strong>{hovered.life} Jahre</strong></div>
          <div class="tooltip-row"><span>Bevölkerung</span><strong>{formatPopulation(hovered.population)}</strong></div>
        </div>
      {/if}
    </div>
  </div>