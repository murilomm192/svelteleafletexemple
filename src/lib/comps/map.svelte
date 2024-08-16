<script>
  import { onMount, onDestroy } from "svelte";

  function randomIntFromInterval(min, max) {
    // min and max included
    return Math.random() * (max - min + 1) + min;
  }
  let mapElement;
  let map;
  let cluster;

  let points = Array.from(Array(100000).keys()).map(() => [
    randomIntFromInterval(-4, -33),
    randomIntFromInterval(-34, -69),
  ]);

  onMount(async () => {
    const leaflet = await import("leaflet");
    const { MarkerClusterGroup } = await import("leaflet.markercluster");

    map = leaflet.map(mapElement).setView(
      [
        points.reduce((sum, value) => {
          return sum + value[0];
        }, 0) / points.length,
        [
          points.reduce((sum, value) => {
            return sum + value[1];
          }, 0) / points.length,
        ],
      ],
      5,
    );

    leaflet
      .tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution:
          '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
      })
      .addTo(map);

    let markers = new MarkerClusterGroup();

    points.forEach((point) => markers.addLayer(leaflet.marker(point)));

    map.addLayer(markers);
  });

  onDestroy(async () => {
    if (map) {
      console.log("Unloading Leaflet map.");
      map.remove();
    }
  });
</script>

<main>
  <div bind:this={mapElement}></div>
</main>

<style>
  @import "leaflet/dist/leaflet.css";
  @import "leaflet.markercluster/dist/MarkerCluster.Default.css";
  main div {
    height: 800px;
    width: 800px;
  }
</style>
