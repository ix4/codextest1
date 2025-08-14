# Live Location

This page requests your location and displays several representations:

- Decimal coordinates
- Degrees, minutes & seconds
- what3words address
- Plus Code
- Elevation
- Heading

<div id="status">Requesting location…</div>
<pre id="output"></pre>

<script src="https://cdn.jsdelivr.net/npm/open-location-code@1.0.3/openlocationcode.min.js"></script>
<script>
function toDMS(deg) {
  const abs = Math.abs(deg);
  const d = Math.floor(abs);
  const m = Math.floor((abs - d) * 60);
  const s = ((abs - d - m / 60) * 3600).toFixed(2);
  const sign = deg < 0 ? '-' : '';
  return `${sign}${d}°${m}'${s}"`;
}

function updatePosition(pos) {
  const lat = pos.coords.latitude;
  const lng = pos.coords.longitude;
  const alt = pos.coords.altitude;
  const heading = pos.coords.heading;

  let text = '';
  text += `Decimal: ${lat.toFixed(6)}, ${lng.toFixed(6)}\n`;
  text += `DMS: ${toDMS(lat)} ${toDMS(lng)}\n`;
  text += `Plus Code: ${OpenLocationCode.encode(lat, lng)}\n`;
  text += `Elevation: ${alt != null ? alt.toFixed(1) + ' m' : 'unavailable'}\n`;
  text += `Heading: ${heading != null ? heading.toFixed(0) + '°' : 'unavailable'}\n`;
  document.getElementById('output').innerText = text;

  fetch(`https://api.what3words.com/v3/convert-to-3wa?coordinates=${lat},${lng}&key=YOUR_API_KEY`)
    .then(r => r.json())
    .then(data => {
      document.getElementById('output').innerText += `what3words: ${data.words}\n`;
    })
    .catch(() => {
      document.getElementById('output').innerText += 'what3words: unavailable\n';
    });
}

navigator.geolocation.watchPosition(updatePosition, (err) => {
  document.getElementById('status').innerText = 'Error: ' + err.message;
}, { enableHighAccuracy: true });
</script>

