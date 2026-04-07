<script>
    import { arc, pie } from 'd3-shape';
    import { scaleOrdinal } from 'd3-scale';
    import { sum } from 'd3-array';
    import { fade } from 'svelte/transition';
  
    let { data = [] } = $props();
  
    const width = 420;
    const height = 420;
    const radius = Math.min(width, height) / 2 - 20;
  
    const grouped = Object.entries(
      data.reduce((acc, item) => {
        acc[item.continent] = (acc[item.continent] || 0) + item.population;
        return acc;
      }, {})
    ).map(([continent, population]) => ({
      continent,
      population
    }));
  
    const total = sum(grouped, (d) => d.population);
  
    const colorScale = scaleOrdinal()
      .domain(grouped.map((d) => d.continent))
      .range(['#4f8cff', '#22d3ee', '#a855f7', '#f97316', '#ef4444', '#22c55e']);
  
    const pieGenerator = pie()
      .sort(null)
      .value((d) => d.population);
  
    const arcData = pieGenerator(grouped);
  
    const arcGenerator = arc()
      .innerRadius(radius * 0.58)
      .outerRadius(radius);
  
    const labelArc = arc()
      .innerRadius(radius * 0.78)
      .outerRadius(radius * 0.78);
  
    function formatPopulation(value) {
      return new Intl.NumberFormat('de-DE').format(Math.round(value)) + ' Mio.';
    }
  
    function formatPercent(value) {
      if (!total) return '0%';
      return ((value / total) * 100).toFixed(1) + '%';
    }
  </script>
  
  <div class="donut-shell">
    <div class="donut-header">
      <div>
        <p class="mini-chart-label">Chart 03</p>
        <h3>Population Share</h3>
      </div>
      <p class="mini-chart-copy">Bevölkerungsanteile nach Kontinent</p>
    </div>
  
    <div class="donut-layout">
      <svg viewBox={`0 0 ${width} ${height}`} class="donut-svg" aria-label="Doughnut chart">
        <g transform={`translate(${width / 2}, ${height / 2})`}>
          {#each arcData as slice, i}
            <path
              in:fade={{ duration: 220 + i * 40 }}
              d={arcGenerator(slice)}
              fill={colorScale(slice.data.continent)}
              class="donut-slice"
            />
          {/each}
  
          <circle r={radius * 0.45} class="donut-center" />
          <text y="-8" text-anchor="middle" class="donut-center-label">Gesamt</text>
          <text y="20" text-anchor="middle" class="donut-center-value">
            {formatPopulation(total)}
          </text>
        </g>
      </svg>
  
      <div class="donut-legend">
        {#each grouped as item}
          <div class="donut-legend-item">
            <div class="donut-legend-left">
              <span
                class="donut-legend-dot"
                style={`background:${colorScale(item.continent)};`}
              ></span>
              <span>{item.continent}</span>
            </div>
  
            <div class="donut-legend-right">
              <strong>{formatPercent(item.population)}</strong>
              <small>{formatPopulation(item.population)}</small>
            </div>
          </div>
        {/each}
      </div>
    </div>
  </div>