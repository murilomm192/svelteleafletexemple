<script>
  import { onMount, onDestroy } from "svelte";

  function randomIntFromInterval(min, max) {
    // min and max included
    return Math.random() * (max - min + 1) + min;
  }
  let mapElement;
  let map;

  let points = Array.from(Array(1000).keys()).map(() => [
    randomIntFromInterval(-13, -16),
    randomIntFromInterval(-43, -46),
  ]);

  onMount(async () => {
    const leaflet = await import("leaflet");

    map = leaflet.map(mapElement).setView([-15, -44], 13);

    leaflet
      .tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution:
          '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
      })
      .addTo(map);

    points.forEach((point) => leaflet.marker(point).addTo(map));
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
  main div {
    height: 800px;
  }
</style>
