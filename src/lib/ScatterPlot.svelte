<script>
  import { scaleLinear, scaleSqrt, scaleOrdinal } from 'd3-scale';
  import { fade } from 'svelte/transition';

  let { data = [] } = $props();

  const width = 900;
  const height = 520;

  const margin = {
    top: 20,
    right: 20,
    bottom: 65,
    left: 70
  };

  const innerWidth = width - margin.left - margin.right;
  const innerHeight = height - margin.top - margin.bottom;

  let hovered = $state(null);
  let tooltipX = $state(0);
  let tooltipY = $state(0);

  const allContinents = ['Europe', 'North America', 'South America', 'Asia', 'Africa', 'Oceania'];

  const colorScale = scaleOrdinal()
    .domain(allContinents)
    .range(['#3b82f6', '#14b8a6', '#f59e0b', '#ef4444', '#8b5cf6', '#22c55e']);

  const xScale = scaleLinear()
    .domain([0, 90000])
    .range([0, innerWidth]);

  const yScale = scaleLinear()
    .domain([50, 86])
    .range([innerHeight, 0]);

  const rScale = scaleSqrt()
    .domain([0, 1500])
    .range([6, 24]);

  const xTicks = [0, 20000, 40000, 60000, 80000];
  const yTicks = [50, 55, 60, 65, 70, 75, 80, 85];

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

<div class="scatter-shell">
  <div class="legend">
    {#each allContinents as continent}
      <div class="legend-item">
        <span
          class="legend-dot"
          style={`background-color:${colorScale(continent)};`}
        ></span>
        <span>{continent}</span>
      </div>
    {/each}
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

        <text x={innerWidth / 2} y={innerHeight + 50} text-anchor="middle" class="axis-label">
          BIP pro Kopf (USD)
        </text>

        <text
          transform={`translate(-48, ${innerHeight / 2}) rotate(-90)`}
          text-anchor="middle"
          class="axis-label"
        >
          Lebenserwartung (Jahre)
        </text>

        {#each data as d (d.country)}
          <circle
            in:fade={{ duration: 220 }}
            out:fade={{ duration: 160 }}
            cx={xScale(d.gdp)}
            cy={yScale(d.life)}
            r={rScale(d.population)}
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
        style={`left:${tooltipX + 14}px; top:${tooltipY - 14}px;`}
      >
        <div class="tooltip-title">{hovered.country}</div>
        <div class="tooltip-row"><span>Kontinent</span><strong>{hovered.continent}</strong></div>
        <div class="tooltip-row"><span>BIP</span><strong>{formatGDP(hovered.gdp)}</strong></div>
        <div class="tooltip-row"><span>Lebenserwartung</span><strong>{hovered.life} Jahre</strong></div>
        <div class="tooltip-row"><span>Bevölkerung</span><strong>{formatPopulation(hovered.population)}</strong></div>
      </div>
    {/if}
  </div>
</div>