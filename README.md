<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8" />
  <title>Mappa gruppi automobilistici cinesi</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <!-- Leaflet CSS -->
  <link
    rel="stylesheet"
    href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
    integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
    crossorigin=""
  />

  <style>
    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    }
    #map {
      width: 100%;
      height: 100vh;
    }

    .group-tooltip {
      font-size: 13px;
      line-height: 1.4;
    }
    .group-tooltip strong {
      font-size: 14px;
    }

    .group-popup {
      font-size: 13px;
      line-height: 1.5;
      max-width: 260px;
    }
    .group-popup h3 {
      margin: 0 0 4px;
      font-size: 16px;
    }
    .group-popup ul {
      padding-left: 18px;
      margin: 4px 0;
    }
    .group-popup li {
      margin-bottom: 2px;
    }

    /* marker “ingrandito” al passaggio del mouse */
    .leaflet-marker-icon.group-marker {
      transition: transform 0.15s ease;
      transform-origin: center bottom;
    }
    .leaflet-marker-icon.group-marker.hovered {
      transform: scale(1.25);
      z-index: 999 !important;
    }
  </style>
</head>
<body>
  <div id="map"></div>

  <!-- Leaflet JS -->
  <script
    src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
    integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
    crossorigin=""
  ></script>

  <script>
    // Inizializza la mappa centrata sulla Cina
    const map = L.map('map', {
      minZoom: 3,
      maxZoom: 8
    }).setView([35, 104], 4); // lat, lng generici per la Cina

    // Tile layer base (OpenStreetMap)
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; OpenStreetMap contributors'
    }).addTo(map);

    // DATI GRUPPI – puoi ampliarli/ritoccarli
    const groups = [
      {
        name: "Chery Automobile",
        city: "Wuhu (Anhui)",
        coords: [31.33, 118.38],
        brands: ["Chery", "Exeed", "Jetour", "Karry"],
        notes: "Chery è uno dei principali esportatori cinesi, forte nei mercati emergenti e in rapida espansione in Europa."
      },
      {
        name: "Geely Holding Group",
        city: "Hangzhou (Zhejiang)",
        coords: [30.27, 120.15],
        brands: ["Geely", "Lynk & Co", "Zeekr", "Geometry", "Lotus (controllo)", "Volvo Cars (controllo)"],
        notes: "Gruppo estremamente attivo a livello globale, con partecipazioni in Volvo Cars, Lotus, Smart e altri marchi internazionali."
      },
      {
        name: "SAIC Motor",
        city: "Shanghai",
        coords: [31.23, 121.47],
        brands: ["MG", "Roewe", "Maxus", "IM Motors"],
        notes: "Tra i più grandi gruppi cinesi, molto presente in Europa con il marchio MG."
      },
      {
        name: "BYD Auto",
        city: "Shenzhen (Guangdong)",
        coords: [22.54, 114.06],
        brands: ["BYD", "Denza", "Yangwang", "Fangchengbao"],
        notes: "Specialista in elettrico e ibrido plug-in, fra i principali produttori di veicoli a nuova energia al mondo."
      },
      {
        name: "GAC Group",
        city: "Guangzhou (Guangdong)",
        coords: [23.13, 113.26],
        brands: ["GAC Aion", "Trumpchi (GAC Motor)", "Hino (JV)", "Mitsubishi (JV)"],
        notes: "Gruppo radicato nel sud della Cina, attivo anche con joint-venture con marchi giapponesi."
      },
      {
        name: "Great Wall Motor",
        city: "Baoding (Hebei)",
        coords: [38.87, 115.48],
        brands: ["Haval", "Wey", "Ora", "Tank"],
        notes: "Molto forte nei SUV e nei pick-up, sta spingendo su elettrico e ibrido con sub-brand dedicati."
      },
      {
        name: "FAW Group",
        city: "Changchun (Jilin)",
        coords: [43.90, 125.32],
        brands: ["Hongqi", "Bestune", "FAW Jiefang"],
        notes: "Uno dei gruppi storici, legato anche a joint-venture con marchi tedeschi e giapponesi."
      },
      {
        name: "BAIC Group",
        city: "Pechino",
        coords: [39.90, 116.40],
        brands: ["Beijing", "Arcfox", "Foton"],
        notes: "Importante player statale con forte presenza nei veicoli commerciali e nelle JV con marchi stranieri."
      }
    ];

    // Funzione per creare il contenuto del tooltip (hover)
    function buildTooltipContent(group) {
      const brandsList = group.brands.join(", ");
      return `
        <div class="group-tooltip">
          <strong>${group.name}</strong><br/>
          <span>${group.city}</span><br/>
          <em>Brand:</em> ${brandsList}
        </div>
      `;
    }

    // Funzione per il popup (click)
    function buildPopupContent(group) {
      const brandsItems = group.brands.map(b => `<li>${b}</li>`).join("");
      return `
        <div class="group-popup">
          <h3>${group.name}</h3>
          <p><strong>Sede:</strong> ${group.city}</p>
          <p><strong>Brand principali:</strong></p>
          <ul>${brandsItems}</ul>
          ${group.notes ? `<p>${group.notes}</p>` : ""}
        </div>
      `;
    }

    // Aggiunge i marker
    groups.forEach(group => {
      const marker = L.marker(group.coords, {
        title: group.name
      }).addTo(map);

      // Aggiungi una classe per l'effetto hover
      marker.on('add', function() {
        const icon = marker._icon;
        if (icon) {
          icon.classList.add('group-marker');
        }
      });

      // Tooltip al passaggio del mouse
      marker.bindTooltip(buildTooltipContent(group), {
        direction: "top",
        offset: [0, -10],
        sticky: true,
        opacity: 0.95
      });

      // Popup al clic
      marker.bindPopup(buildPopupContent(group));

      // Effetto "ingrandimento" sul marker al passaggio del mouse
      marker.on('mouseover', function() {
        if (marker._icon) {
          marker._icon.classList.add('hovered');
        }
      });
      marker.on('mouseout', function() {
        if (marker._icon) {
          marker._icon.classList.remove('hovered');
        }
      });
    });
  </script>
</body>
</html>
