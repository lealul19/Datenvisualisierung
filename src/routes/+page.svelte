<script>
    import ScatterPlot from '$lib/ScatterPlot.svelte';
    import { countryData } from '$lib/data.js';
  </script>
  
  <svelte:head>
    <title>Interaktive Datenvisualisierung | D3.js + Svelte</title>
    <meta
      name="description"
      content="Professioneller interaktiver Scatterplot mit D3.js, SVG und Svelte."
    />
  </svelte:head>
  
  <div class="page-shell">
    <section class="hero">
      <div class="hero-content">
        <div class="hero-badge">Projekt · 1 Person · D3.js + SVG + Svelte</div>
        <h1>Interaktive Datenvisualisierung mit intelligentem Scatterplot</h1>
        <p class="hero-text">
          Dieses Projekt visualisiert den Zusammenhang zwischen
          <strong>BIP pro Kopf</strong> und <strong>Lebenserwartung</strong>.
          Nutzer können gezielt nach Kontinenten filtern, Länder analysieren und
          über eine interaktive Oberfläche schneller Erkenntnisse gewinnen.
        </p>
      </div>
    </section>
  
    <section class="info-grid">
      <article class="info-card">
        <span class="card-label">Analyse</span>
        <h2>Klare Datenzusammenhänge</h2>
        <p>
          Der Scatterplot zeigt sofort, wie wirtschaftliche und soziale Kennzahlen
          zusammenhängen.
        </p>
      </article>
  
      <article class="info-card">
        <span class="card-label">Interaktivität</span>
        <h2>Filter, Hover und Animation</h2>
        <p>
          Kontinente lassen sich per Button auswählen. Beim Überfahren erscheinen
          detaillierte Informationen im Tooltip.
        </p>
      </article>
  
      <article class="info-card">
        <span class="card-label">Technologie</span>
        <h2>D3.js + SVG + Svelte</h2>
        <p>
          D3.js übernimmt Skalierung und Logik, SVG die Visualisierung und Svelte
          die reaktive Benutzeroberfläche.
        </p>
      </article>
    </section>
  
    <section class="chart-panel">
      <div class="section-head">
        <div>
          <p class="eyebrow">Interaktive Visualisierung</p>
          <h2>Scatterplot mit Kontinent-Filter</h2>
        </div>
        <p class="section-copy">
          X-Achse: BIP pro Kopf · Y-Achse: Lebenserwartung · Größe: Bevölkerung
        </p>
      </div>
  
      <ScatterPlot data={countryData} />
    </section>
  </div>