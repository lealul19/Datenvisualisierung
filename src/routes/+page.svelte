<script>
  import { mean, max } from 'd3-array';
  import ScatterPlot from '$lib/ScatterPlot.svelte';
  import LollipopChart from '$lib/LollipopChart.svelte';
  import DoughnutChart from '$lib/DoughnutChart.svelte';
  import ColumnChart from '$lib/ColumnChart.svelte';
  import { countryData } from '$lib/data.js';

  let activeContinent = $state('All');

  const continents = [...new Set(countryData.map((d) => d.continent))];

  const filteredData = $derived(
    activeContinent === 'All'
      ? countryData
      : countryData.filter((d) => d.continent === activeContinent)
  );

  const rankingData = $derived(
    [...filteredData].sort((a, b) => b.life - a.life).slice(0, 8)
  );

  const stats = $derived({
    countries: filteredData.length,
    avgLife: filteredData.length ? mean(filteredData, (d) => d.life).toFixed(1) : '0.0',
    maxGdp: filteredData.length
      ? Math.round((max(filteredData, (d) => d.gdp) || 0) / 1000) + 'k'
      : '0k',
    totalPopulation: filteredData.length
      ? Math.round(filteredData.reduce((sum, d) => sum + d.population, 0)) + ' Mio.'
      : '0 Mio.'
  });

  function setFilter(continent) {
    activeContinent = continent;
  }
</script>

<svelte:head>
  <title>Interactive Data Dashboard</title>
</svelte:head>

<div class="page-shell">
  <section class="hero-panel premium-hero">
    <div class="hero-badge">D3.js · SVG · Svelte</div>

    <div class="hero-grid">
      <div class="hero-main">
        <h1>Country Data Dashboard</h1>
        <p class="hero-copy">
          Analysiere <strong>BIP</strong>, <strong>Lebenserwartung</strong> und
          <strong>Bevölkerung</strong> in einem modernen Dashboard.
        </p>
      </div>

      <div class="hero-side">
        <div class="mini-note special-note">
          <span class="mini-label">Aktiver Filter</span>
          <strong>{activeContinent === 'All' ? 'Alle Kontinente' : activeContinent}</strong>
        </div>
      </div>
    </div>
  </section>

  <section class="controls-panel premium-controls">
    <div class="controls-head">
      <h2>Kontinent auswählen</h2>
    </div>

    <div class="filter-row">
      <button
        class="filter-btn"
        class:active-filter={activeContinent === 'All'}
        type="button"
        onclick={() => setFilter('All')}
      >
        Alle
      </button>

      {#each continents as continent}
        <button
          class="filter-btn"
          class:active-filter={activeContinent === continent}
          type="button"
          onclick={() => setFilter(continent)}
        >
          {continent}
        </button>
      {/each}
    </div>
  </section>

  <section class="stats-grid premium-stats">
    <div class="stat-card glow-card">
      <span class="stat-label">Länder</span>
      <strong>{stats.countries}</strong>
    </div>

    <div class="stat-card glow-card">
      <span class="stat-label">Ø Lebenserwartung</span>
      <strong>{stats.avgLife} Jahre</strong>
    </div>

    <div class="stat-card glow-card">
      <span class="stat-label">Höchstes BIP</span>
      <strong>{stats.maxGdp}</strong>
    </div>

    <div class="stat-card glow-card">
      <span class="stat-label">Gesamtbevölkerung</span>
      <strong>{stats.totalPopulation} </strong>
    </div>
  </section>

  <section class="dashboard-grid dashboard-grid-premium">
    <div class="panel panel-large premium-panel">
      <div class="panel-head">
        <h2>Scatterplot</h2>
        <p class="panel-copy">BIP vs. Lebenserwartung</p>
      </div>

      <ScatterPlot data={filteredData} />
    </div>

    <div class="right-column">
      <div class="panel premium-panel">
        <DoughnutChart data={countryData} />
      </div>

      <div class="panel premium-panel panel-small">
        <div class="panel-head">
          <h2>Top Länder</h2>
          <p class="panel-copy">Nach Lebenserwartung</p>
        </div>

        <LollipopChart data={rankingData} />
      </div>
    </div>
  </section>

  <section class="bottom-grid">
    <div class="panel premium-panel big-bottom-panel">
      <ColumnChart data={filteredData} metric="gdp" />
    </div>
  </section>
</div>