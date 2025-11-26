<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Mythic World Map</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }
    
    body {
      margin: 0;
      background: linear-gradient(135deg, #0c0c1d, #1a1a2e, #0f0f23);
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      color: #e0e0ff;
      overflow: hidden;
      height: 100vh;
    }
    
    .container {
      display: flex;
      flex-direction: column;
      height: 100vh;
      position: relative;
    }
    
    .header {
      text-align: center;
      padding: 15px 0;
      background: rgba(10, 15, 30, 0.9);
      border-bottom: 2px solid #8a6d3b;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.7);
      z-index: 1000;
      position: relative;
    }
    
    h1 {
      font-size: 2.8rem;
      background: linear-gradient(to right, #d4af37, #f9e49a, #d4af37);
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
      text-shadow: 0 0 10px rgba(212, 175, 55, 0.3);
      letter-spacing: 2px;
      margin-bottom: 5px;
      font-family: 'Trajan Pro', serif;
    }
    
    .map-container {
      flex: 1;
      position: relative;
      overflow: hidden;
      border-top: 3px solid #5d4037;
      border-bottom: 3px solid #5d4037;
    }
    
    #map {
      height: 100%;
      width: 100%;
      background: #0a0f1f;
    }
    
    .controls {
      position: absolute;
      top: 20px;
      right: 20px;
      z-index: 1000;
      display: flex;
      flex-direction: column;
      gap: 12px;
    }
    
    .control-btn {
      background: rgba(20, 25, 50, 0.9);
      border: 1px solid #8a6d3b;
      color: #d4af37;
      width: 40px;
      height: 40px;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      font-size: 18px;
      box-shadow: 0 0 8px rgba(0, 0, 0, 0.5);
      transition: all 0.3s ease;
    }
    
    .control-btn:hover {
      background: rgba(40, 45, 80, 0.95);
      transform: scale(1.1);
      box-shadow: 0 0 12px rgba(212, 175, 55, 0.5);
    }
    
    .footer {
      text-align: center;
      padding: 12px;
      background: rgba(10, 15, 30, 0.9);
      border-top: 2px solid #8a6d37;
      font-size: 0.9rem;
      color: #a0a0d0;
    }
    
    .footer a {
      color: #d4af37;
      text-decoration: none;
    }
    
    .footer a:hover {
      text-decoration: underline;
    }
    
    .compass {
      position: absolute;
      bottom: 20px;
      right: 20px;
      width: 70px;
      height: 70px;
      background: rgba(20, 25, 50, 0.9);
      border-radius: 50%;
      border: 2px solid #8a6d37;
      display: flex;
      align-items: center;
      justify-content: center;
      box-shadow: 0 0 15px rgba(0, 0, 0, 0.6);
      z-index: 1000;
    }
    
    .compass-inner {
      position: relative;
      width: 50px;
      height: 50px;
    }
    
    .compass-direction {
      position: absolute;
      font-size: 12px;
      font-weight: bold;
      color: #d4af37;
    }
    
    .north { top: -15px; left: 50%; transform: translateX(-50%); }
    .south { bottom: -15px; left: 50%; transform: translateX(-50%); }
    .east { right: -15px; top: 50%; transform: translateY(-50%); }
    .west { left: -15px; top: 50%; transform: translateY(-50%); }
    
    .compass-needle {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 4px;
      height: 25px;
      background: #d4af37;
      transform-origin: top center;
      transform: translate(-50%, -100%) rotate(0deg);
      border-radius: 2px;
      box-shadow: 0 0 5px rgba(212, 175, 55, 0.7);
    }
    
    .compass-needle::after {
      content: '';
      position: absolute;
      bottom: -5px;
      left: 50%;
      transform: translateX(-50%);
      width: 0;
      height: 0;
      border-left: 6px solid transparent;
      border-right: 6px solid transparent;
      border-top: 10px solid #d4af37;
    }
    
    .compass-center {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 12px;
      height: 12px;
      background: #8a6d37;
      border-radius: 50%;
      transform: translate(-50%, -50%);
      z-index: 10;
    }
    
    /* Custom map styling */
    .leaflet-container {
      background: #0a0f1f;
    }
    
    .leaflet-control-attribution {
      background: rgba(20, 25, 50, 0.9) !important;
      color: #d4af37 !important;
      border: 1px solid #8a6d37 !important;
    }
    
    .leaflet-control-attribution a {
      color: #a0c0ff !important;
    }
    
    /* Traveling icons */
    .traveling-icon {
      position: absolute;
      z-index: 1000;
      font-size: 24px;
      text-shadow: 0 0 8px rgba(255, 255, 255, 0.7);
      transition: transform 0.3s ease;
      cursor: pointer;
    }
    
    .traveling-icon.ship {
      color: #4d79ff;
    }
    
    .traveling-icon.plane {
      color: #ff6666;
    }
    
    .traveling-icon.ufo {
      color: #66ff66;
    }
    
    .traveling-icon.rocket {
      color: #ffcc00;
    }
    
    .traveling-icon.satellite {
      color: #cc66ff;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>MYTHIC WORLD MAP</h1>
    </div>
    
    <div class="map-container">
      <div id="map"></div>
      
      <div class="controls">
        <div class="control-btn" id="zoom-in" title="Zoom In">
          <i class="fas fa-plus"></i>
        </div>
        <div class="control-btn" id="zoom-out" title="Zoom Out">
          <i class="fas fa-minus"></i>
        </div>
        <div class="control-btn" id="reset-view" title="Reset View">
          <i class="fas fa-globe"></i>
        </div>
      </div>
      
      <div class="compass">
        <div class="compass-inner">
          <div class="compass-direction north">N</div>
          <div class="compass-direction south">S</div>
          <div class="compass-direction east">E</div>
          <div class="compass-direction west">W</div>
          <div class="compass-needle" id="compass-needle"></div>
          <div class="compass-center"></div>
        </div>
      </div>
    </div>
    
    <div class="footer">
      <p>Mythic World Map &copy; 2023 | <a href="#">About</a></p>
    </div>
  </div>

  <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
  <script>
    // Initialize the map
    const map = L.map('map').setView([20, 0], 3);
    
    // Add custom tile layer with fantasy styling
    L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
      attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>',
      maxZoom: 18
    }).addTo(map);
    
    // Add control functionality
    document.getElementById('zoom-in').addEventListener('click', function() {
      map.zoomIn();
    });
    
    document.getElementById('zoom-out').addEventListener('click', function() {
      map.zoomOut();
    });
    
    document.getElementById('reset-view').addEventListener('click', function() {
      map.setView([20, 0], 3);
    });
    
    // Update compass based on map rotation
    map.on('rotate', function() {
      const bearing = map.getBearing();
      document.getElementById('compass-needle').style.transform = `translate(-50%, -100%) rotate(${bearing}deg)`;
    });
    
    // Traveling icons implementation
    const links = [
      {url: "https://angrydroid.neocities.org/", icon: "ship", start: [35, 45], end: [45, -15]},
      {url: "https://angrydroid.neocities.org/angrydroidwebsite/", icon: "plane", start: [45, -15], end: [25, -90]},
      {url: "https://angrydroid.neocities.org/coder/", icon: "ufo", start: [25, -90], end: [15, 30]},
      {url: "https://angrydroid.neocities.org/fortune_fairy/", icon: "rocket", start: [15, 30], end: [60, 120]},
      {url: "https://angrydroid.neocities.org/hacker_news/", icon: "satellite", start: [60, 120], end: [-30, -60]},
      {url: "https://angrydroid.neocities.org/matrix_encryptor/", icon: "ship", start: [-30, -60], end: [35, 45]},
      {url: "https://angrydroid.neocities.org/mythic_radar/", icon: "plane", start: [35, 45], end: [10, 100]},
      {url: "https://angrydroid.neocities.org/mythic_vault_uplink/", icon: "ufo", start: [10, 100], end: [-20, -100]},
      {url: "https://angrydroid.neocities.org/mythicos/", icon: "rocket", start: [-20, -100], end: [35, 45]},
      {url: "https://angrydroid.neocities.org/neural_browser/", icon: "satellite", start: [35, 45], end: [45, -15]},
      {url: "https://angrydroid.neocities.org/neural_chat/", icon: "ship", start: [45, -15], end: [25, -90]},
      {url: "https://angrydroid.neocities.org/security_wizard/", icon: "plane", start: [25, -90], end: [15, 30]},
      {url: "https://angrydroid.neocities.org/securityscanner/", icon: "ufo", start: [15, 30], end: [60, 120]},
      {url: "https://angrydroid.neocities.org/swamp_blog/", icon: "rocket", start: [60, 120], end: [-30, -60]},
      {url: "https://angrydroid.neocities.org/worldmap/", icon: "satellite", start: [-30, -60], end: [35, 45]},
      {url: "https://angrydroid.neocities.org/crt/", icon: "ship", start: [35, 45], end: [10, 100]},
      {url: "https://angrydroid.neocities.org/glitch_purge/", icon: "plane", start: [10, 100], end: [-20, -100]},
      {url: "https://angrydroid.neocities.org/mythic_notepad/", icon: "ufo", start: [-20, -100], end: [35, 45]},
      {url: "https://archive.org/details/mythic-capsule", icon: "rocket", start: [35, 45], end: [45, -15]},
      {url: "https://angrydroid.neocities.org/secrets/", icon: "satellite", start: [45, -15], end: [25, -90]},
      {url: "https://angrydroid.neocities.org/mythic_media_player/media_player", icon: "ship", start: [25, -90], end: [15, 30]},
      {url: "https://angrydroid.neocities.org/chat/", icon: "plane", start: [15, 30], end: [60, 120]},
      {url: "https://angrydroid.neocities.org/web_ring/", icon: "ufo", start: [60, 120], end: [-30, -60]},
      {url: "https://discord.gg/hJdtSEraG5", icon: "rocket", start: [-30, -60], end: [35, 45]},
      {url: "mailto:angrydroid@offilive.com", icon: "satellite", start: [35, 45], end: [10, 100]}
    ];
    
    // Create traveling icons
    function createTravelingIcon(link) {
      const icon = document.createElement('div');
      icon.className = `traveling-icon ${link.icon}`;
      icon.innerHTML = `<i class="fas fa-${link.icon}"></i>`;
      icon.style.position = 'absolute';
      icon.style.zIndex = '1000';
      icon.style.fontSize = '24px';
      icon.style.textShadow = '0 0 8px rgba(255, 255, 255, 0.7)';
      icon.style.cursor = 'pointer';
      
      // Add click event to open link
      icon.addEventListener('click', () => {
        if (link.url.startsWith('mailto:')) {
          window.location.href = link.url;
        } else {
          window.open(link.url, '_blank');
        }
      });
      
      document.querySelector('.map-container').appendChild(icon);
      return icon;
    }
    
    // Animate traveling icons
    function animateIcon(icon, start, end) {
      const startTime = Date.now();
      const duration = 8000 + Math.random() * 4000; // 8-12 seconds
      
      function updatePosition() {
        const elapsed = Date.now() - startTime;
        const progress = Math.min(elapsed / duration, 1);
        
        // Calculate current position
        const lat = start[0] + (end[0] - start[0]) * progress;
        const lng = start[1] + (end[1] - start[1]) * progress;
        
        // Convert lat/lng to screen coordinates
        const point = map.latLngToContainerPoint([lat, lng]);
        icon.style.left = `${point.x}px`;
        icon.style.top = `${point.y}px`;
        
        // Add a little bounce effect
        const bounce = Math.sin(progress * Math.PI) * 10;
        icon.style.transform = `translateY(${-bounce}px)`;
        
        if (progress < 1) {
          requestAnimationFrame(updatePosition);
        } else {
          // Reset animation
          setTimeout(() => {
            animateIcon(icon, end, start);
          }, 1000);
        }
      }
      
      updatePosition();
    }
    
    // Initialize traveling icons
    links.forEach(link => {
      const icon = createTravelingIcon(link);
      animateIcon(icon, link.start, link.end);
    });
  </script>
</body>
</html>
