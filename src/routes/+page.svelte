<script>
    import ScatterPlot from '$lib/ScatterPlot.svelte';
    import { countryData } from '$lib/data.js';
  </script>
  
  <svelte:head>
    <title>Interaktiver Scatterplot | D3.js + Svelte</title>
    <meta
      name="description"
      content="Professionelle Datenvisualisierung mit D3.js, SVG und Svelte."
    />
  </svelte:head>
  
  <div class="page-shell">
    <section class="hero">
      <div class="hero-content">
        <div class="hero-badge">Projekt · 1 Person · D3.js + SVG + Svelte</div>
        <h1>Interaktive Datenvisualisierung mit einem Scatterplot</h1>
        <p class="hero-text">
          Dieses Projekt zeigt eine moderne und interaktive Datenvisualisierung mit
          <strong>D3.js</strong>, <strong>SVG</strong> und <strong>Svelte</strong>.
          Dargestellt wird der Zusammenhang zwischen BIP pro Kopf und
          Lebenserwartung. Die Punktgröße steht für die Bevölkerungszahl.
        </p>
      </div>
    </section>
  
    <section class="info-grid">
      <article class="info-card">
        <span class="card-label">Projektziel</span>
        <h2>Visuelle Analyse von Zusammenhängen</h2>
        <p>
          Der Scatterplot macht Zusammenhänge zwischen wirtschaftlichen und sozialen
          Indikatoren sichtbar und unterstützt eine verständliche Dateninterpretation.
        </p>
      </article>
  
      <article class="info-card">
        <span class="card-label">Technologien</span>
        <h2>D3.js, SVG und Svelte</h2>
        <p>
          D3.js übernimmt Skalierung und Datenlogik, SVG die grafische Darstellung
          und Svelte die reaktive Benutzeroberfläche.
        </p>
      </article>
  
      <article class="info-card">
        <span class="card-label">Interaktivität</span>
        <h2>Tooltip, Farben und Größen</h2>
        <p>
          Beim Hover werden zusätzliche Informationen angezeigt. Farben unterscheiden
          Kontinente, die Punktgröße visualisiert die Bevölkerung.
        </p>
      </article>
    </section>
  
    <section class="chart-panel">
      <div class="section-head">
        <div>
          <p class="eyebrow">Datenvisualisierung</p>
          <h2>Scatterplot der Länder im Vergleich</h2>
        </div>
        <p class="section-copy">
          X-Achse: BIP pro Kopf · Y-Achse: Lebenserwartung · Größe: Bevölkerung
        </p>
      </div>
  
      <ScatterPlot data={countryData} />
    </section>
  </div>