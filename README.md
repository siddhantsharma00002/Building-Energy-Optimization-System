# Building-Energy-Optimization-System
#A smart occupancy-driven Building Energy Optimization System (BEES) featuring a central low-voltage isolated control box, optocoupler-isolated relays, a 2P AC #contactor, ON/OFF-delay timers, AC compressor cooldown protection, ESP32 firmware, and an interactive 12×12 ft Three.js 3D room simulation.
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>12x12 ft Room Appliance Optimization — 3D Wired Room Simulation</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    body {
      background-color: darkslategray;
      color: white;
      padding: 16px;
    }

    .app-shell {
      max-width: 1280px;
      margin: 0 auto;
      background-color: midnightblue;
      border: 2px solid steelblue;
      border-radius: 14px;
      padding: 18px;
      box-shadow: 0 12px 30px black;
    }

    header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 10px;
      padding-bottom: 12px;
      margin-bottom: 16px;
      border-bottom: 2px solid teal;
    }

    header h1 {
      font-size: 1.35rem;
      color: lightcyan;
    }

    .room-tag {
      background-color: teal;
      color: white;
      padding: 5px 12px;
      border-radius: 20px;
      font-size: 0.85rem;
      font-weight: bold;
    }

    .main-grid {
      display: grid;
      grid-template-columns: 1.55fr 1fr;
      gap: 18px;
    }

    @media (max-width: 960px) {
      .main-grid {
        grid-template-columns: 1fr;
      }
    }

    .panel {
      background-color: darkslategray;
      border: 2px solid steelblue;
      border-radius: 10px;
      padding: 14px;
    }

    #canvasContainer {
      width: 100%;
      height: 470px;
      border-radius: 8px;
      overflow: hidden;
      border: 2px solid silver;
      background-color: black;
      position: relative;
    }

    .canvas-hint {
      position: absolute;
      bottom: 8px;
      left: 10px;
      background-color: rgba(0, 0, 0, 0.72);
      color: lightcyan;
      font-size: 0.78rem;
      padding: 4px 8px;
      border-radius: 4px;
      pointer-events: none;
    }

    .wire-legend {
      position: absolute;
      top: 10px;
      left: 10px;
      background-color: rgba(0, 0, 0, 0.78);
      border: 1px solid steelblue;
      border-radius: 6px;
      padding: 7px 10px;
      font-size: 0.76rem;
      line-height: 1.45;
      pointer-events: none;
    }

    .dot {
      display: inline-block;
      width: 10px;
      height: 10px;
      border-radius: 50%;
      margin-right: 6px;
    }

    .controls-bar {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      margin-top: 12px;
      align-items: center;
    }

    button {
      border: none;
      border-radius: 6px;
      padding: 9px 14px;
      font-weight: bold;
      cursor: pointer;
      font-size: 0.88rem;
    }

    .btn-enter { background-color: mediumseagreen; color: white; }
    .btn-exit { background-color: crimson; color: white; }
    .btn-mode { background-color: dimgray; color: white; }
    .btn-mode.active { background-color: dodgerblue; outline: 2px solid white; }
    .btn-reset { background-color: steelblue; color: white; }

    .section-title {
      font-size: 1rem;
      color: skyblue;
      margin-bottom: 10px;
      padding-bottom: 5px;
      border-bottom: 1px solid steelblue;
    }

    .lcd-box {
      background-color: darkolivegreen;
      color: chartreuse;
      font-family: 'Courier New', Courier, monospace;
      font-weight: bold;
      padding: 10px 12px;
      border-radius: 6px;
      border: 2px solid black;
      margin-bottom: 12px;
      font-size: 0.88rem;
      line-height: 1.45;
    }

    .relay-grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 10px;
      margin-bottom: 14px;
    }

    .relay-card {
      background-color: midnightblue;
      border: 2px solid gray;
      border-radius: 8px;
      padding: 10px;
      font-size: 0.85rem;
    }

    .relay-card.energized {
      border-color: limegreen;
      box-shadow: 0 0 8px limegreen;
    }

    .relay-card.cooldown {
      border-color: orange;
      box-shadow: 0 0 8px orange;
    }

    .relay-top {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-weight: bold;
      margin-bottom: 4px;
    }

    .pill {
      padding: 2px 8px;
      border-radius: 10px;
      font-size: 0.75rem;
      font-weight: bold;
    }

    .pill-on { background-color: forestgreen; color: white; }
    .pill-off { background-color: firebrick; color: white; }
    .pill-lock { background-color: darkorange; color: black; }

    .metrics-grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 10px;
      margin-bottom: 12px;
    }

    .metric-card {
      background-color: black;
      border: 1px solid steelblue;
      border-radius: 8px;
      padding: 10px;
    }

    .metric-label {
      font-size: 0.78rem;
      color: lightgray;
      margin-bottom: 4px;
    }

    .metric-val {
      font-size: 1.1rem;
      font-weight: bold;
      color: lightcyan;
      font-family: 'Courier New', Courier, monospace;
    }

    .log-box {
      background-color: black;
      color: lightgreen;
      font-family: 'Courier New', Courier, monospace;
      font-size: 0.78rem;
      height: 115px;
      overflow-y: auto;
      padding: 8px;
      border-radius: 6px;
      border: 1px solid steelblue;
    }
  </style>
</head>
<body>
  <div class="app-shell">
    <header>
      <h1>Room Appliance Optimization System — 12×12 ft 3D Wired Room Simulation</h1>
      <span class="room-tag">Room: 12 ft × 12 ft • 1s Entry Relay Delay + Highlighted Conduits</span>
    </header>

    <div class="main-grid">
      <!-- Left: 3D Room + User Controls -->
      <div class="panel">
        <div id="canvasContainer">
          <div class="wire-legend">
            <div><span class="dot" style="background: cyan;"></span>Low-Voltage Sensor Wire (mmWave/PIR)</div>
            <div><span class="dot" style="background: gold;"></span>Switched Live Wire: Ceiling Light</div>
            <div><span class="dot" style="background: lime;"></span>Switched Live Wire: Ceiling Fan</div>
            <div><span class="dot" style="background: deepskyblue;"></span>Switched Live Wire: TV Socket</div>
            <div><span class="dot" style="background: orangered;"></span>2P Contactor Live Wire: Air Conditioner</div>
          </div>
          <div class="canvas-hint">Drag to orbit 3D view • Scroll to zoom</div>
        </div>

        <div class="controls-bar">
          <button class="btn-enter" onclick="personEntersRoom()">Person Enters Door</button>
          <button class="btn-exit" onclick="personExitsRoom()">Person Exits Door</button>
          <button class="btn-mode active" id="btnAuto" onclick="setControlMode('AUTO')">Mode: AUTO</button>
          <button class="btn-mode" id="btnForceOn" onclick="setControlMode('FORCE_ON')">FORCE ON</button>
          <button class="btn-mode" id="btnForceOff" onclick="setControlMode('FORCE_OFF')">FORCE OFF</button>
          <button class="btn-reset" onclick="resetCounters()">Reset Stats</button>
        </div>
      </div>

      <!-- Right: Relay Control Panel, Power & Energy Saved -->
      <div class="panel">
        <h2 class="section-title">Central Control Box &amp; Relay Switching</h2>

        <div class="lcd-box" id="lcdScreen">
          OCCUPANCY: OUTSIDE DOOR (EMPTY)<br />
          ON-DELAY: IDLE | OFF-DELAY: IDLE | AC LOCK: READY
        </div>

        <div class="relay-grid">
          <div class="relay-card" id="relayCardLight">
            <div class="relay-top">
              <span>Relay CH1 • Ceiling Light</span>
              <span class="pill pill-off" id="pillLight">OPEN (OFF)</span>
            </div>
            <div>60W LED Light • Gold Live Conduit</div>
          </div>

          <div class="relay-card" id="relayCardFan">
            <div class="relay-top">
              <span>Relay CH2 • Ceiling Fan</span>
              <span class="pill pill-off" id="pillFan">OPEN (OFF)</span>
            </div>
            <div>75W Fan Motor • Green Live Conduit</div>
          </div>

          <div class="relay-card" id="relayCardTv">
            <div class="relay-top">
              <span>Relay CH3 • LED TV</span>
              <span class="pill pill-off" id="pillTv">OPEN (OFF)</span>
            </div>
            <div>120W TV Socket • Blue Live Conduit</div>
          </div>

          <div class="relay-card" id="relayCardAc">
            <div class="relay-top">
              <span>Relay CH4 → 2P Contactor • AC</span>
              <span class="pill pill-off" id="pillAc">OPEN (OFF)</span>
            </div>
            <div id="acRelayNote">1500W AC • Red-Orange Heavy Conduit</div>
          </div>
        </div>

        <h2 class="section-title">Power Drawn &amp; Electricity Saved</h2>
        <div class="metrics-grid">
          <div class="metric-card">
            <div class="metric-label">Current Power Drawn</div>
            <div class="metric-val" id="powerDrawnVal">0 W</div>
          </div>
          <div class="metric-card">
            <div class="metric-label">Uncontrolled Room Load</div>
            <div class="metric-val" id="uncontrolledWattsVal">1755 W</div>
          </div>
          <div class="metric-card">
            <div class="metric-label">Electricity Saved (Wh)</div>
            <div class="metric-val" id="energySavedWhVal">0.00 Wh</div>
          </div>
          <div class="metric-card">
            <div class="metric-label">Electricity Saved (kWh / Units)</div>
            <div class="metric-val" id="energySavedKwhVal">0.0000 kWh</div>
          </div>
        </div>

        <div class="log-box" id="relayLog"></div>
      </div>
    </div>
  </div>

  <!-- Three.js for 3D 12x12 ft decorated room -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
  <script>
    const WATTS = {
      light: 60,
      fan: 75,
      tv: 120,
      ac: 1500
    };

    let mode = "AUTO";
    let personInside = false;
    let doorTargetAngle = 0;
    let personWaypoints = [];
    let onDelayTimer = 0;
    let offDelayTimer = 0;
    let acCooldownTimer = 0;

    let appliancesOn = {
      light: false,
      fan: false,
      tv: false,
      ac: false
    };

    let energySavedWh = 0;

    function logRelay(msg) {
      const box = document.getElementById("relayLog");
      const line = document.createElement("div");
      line.textContent = `[${new Date().toLocaleTimeString()}] ${msg}`;
      box.prepend(line);
    }

    const container = document.getElementById("canvasContainer");
    const scene = new THREE.Scene();
    scene.background = new THREE.Color("darkslategray");

    const camera = new THREE.PerspectiveCamera(
      48,
      container.clientWidth / container.clientHeight,
      0.1,
      100
    );

    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(container.clientWidth, container.clientHeight);
    container.appendChild(renderer.domElement);

    let isDragging = false;
    let prevMouse = { x: 0, y: 0 };
    let camAngle = 0.72;
    let camRadius = 16.5;
    let camHeight = 8.8;

    function updateCameraPos() {
      camera.position.x = Math.sin(camAngle) * camRadius;
      camera.position.z = Math.cos(camAngle) * camRadius;
      camera.position.y = camHeight;
      camera.lookAt(-0.3, 1.8, -0.5);
    }
    updateCameraPos();

    renderer.domElement.addEventListener("mousedown", (e) => {
      isDragging = true;
      prevMouse = { x: e.clientX, y: e.clientY };
    });
    window.addEventListener("mouseup", () => (isDragging = false));
    window.addEventListener("mousemove", (e) => {
      if (!isDragging) return;
      camAngle -= (e.clientX - prevMouse.x) * 0.008;
      camHeight = Math.min(13.5, Math.max(2.5, camHeight + (e.clientY - prevMouse.y) * 0.04));
      prevMouse = { x: e.clientX, y: e.clientY };
      updateCameraPos();
    });
    renderer.domElement.addEventListener("wheel", (e) => {
      camRadius = Math.min(23, Math.max(8.5, camRadius + e.deltaY * 0.01));
      updateCameraPos();
    });

    const ambientLight = new THREE.AmbientLight(0xffffff, 0.22);
    scene.add(ambientLight);

    const mainRoomLight = new THREE.PointLight(0xffe599, 0, 14, 1.6);
    mainRoomLight.position.set(-2.2, 4.35, -0.6);
    scene.add(mainRoomLight);

    const floor = new THREE.Mesh(
      new THREE.PlaneGeometry(12, 12),
      new THREE.MeshStandardMaterial({ color: "burlywood", roughness: 0.78 })
    );
    floor.rotation.x = -Math.PI / 2;
    scene.add(floor);

    const wallMat = new THREE.MeshStandardMaterial({ color: "linen", roughness: 0.9 });
    const backWall = new THREE.Mesh(new THREE.BoxGeometry(12, 4.8, 0.25), wallMat);
    backWall.position.set(0, 2.4, -6);
    scene.add(backWall);

    const leftWallBack = new THREE.Mesh(new THREE.BoxGeometry(0.25, 4.8, 8.2), wallMat);
    leftWallBack.position.set(-6, 2.4, -1.9);
    scene.add(leftWallBack);

    const leftWallFront = new THREE.Mesh(new THREE.BoxGeometry(0.25, 4.8, 1.8), wallMat);
    leftWallFront.position.set(-6, 2.4, 5.1);
    scene.add(leftWallFront);

    const doorHeaderWall = new THREE.Mesh(new THREE.BoxGeometry(0.25, 1.4, 2.0), wallMat);
    doorHeaderWall.position.set(-6, 4.1, 3.2);
    scene.add(doorHeaderWall);

    const frameMat = new THREE.MeshStandardMaterial({ color: "saddlebrown", roughness: 0.7 });
    const post1 = new THREE.Mesh(new THREE.BoxGeometry(0.32, 3.45, 0.14), frameMat);
    post1.position.set(-6, 1.725, 2.2);
    const post2 = new THREE.Mesh(new THREE.BoxGeometry(0.32, 3.45, 0.14), frameMat);
    post2.position.set(-6, 1.725, 4.2);
    const lintel = new THREE.Mesh(new THREE.BoxGeometry(0.32, 0.14, 2.14), frameMat);
    lintel.position.set(-6, 3.42, 3.2);
    scene.add(post1, post2, lintel);

    const doorPivot = new THREE.Group();
    doorPivot.position.set(-5.95, 0, 2.25);
    const doorLeaf = new THREE.Mesh(
      new THREE.BoxGeometry(0.1, 3.32, 1.88),
      new THREE.MeshStandardMaterial({ color: "peru", roughness: 0.65 })
    );
    doorLeaf.position.set(0, 1.66, 0.94);

    const doorHandle = new THREE.Mesh(
      new THREE.SphereGeometry(0.08, 12, 12),
      new THREE.MeshStandardMaterial({ color: "gold", metalness: 0.6 })
    );
    doorHandle.position.set(0.08, 1.55, 1.68);
    doorPivot.add(doorLeaf, doorHandle);
    scene.add(doorPivot);

    const sofaGroup = new THREE.Group();
    const sofaMat = new THREE.MeshStandardMaterial({ color: "steelblue", roughness: 0.75 });
    const seat = new THREE.Mesh(new THREE.BoxGeometry(3.4, 0.6, 1.3), sofaMat);
    seat.position.set(0, 0.3, 0);
    const backrest = new THREE.Mesh(new THREE.BoxGeometry(3.4, 0.9, 0.35), sofaMat);
    backrest.position.set(0, 0.75, -0.48);
    const armLeft = new THREE.Mesh(new THREE.BoxGeometry(0.3, 0.75, 1.3), sofaMat);
    armLeft.position.set(-1.55, 0.375, 0);
    const armRight = new THREE.Mesh(new THREE.BoxGeometry(0.3, 0.75, 1.3), sofaMat);
    armRight.position.set(1.55, 0.375, 0);
    sofaGroup.add(seat, backrest, armLeft, armRight);
    sofaGroup.position.set(-2.5, 0, -2.5);
    sofaGroup.rotation.y = Math.PI / 5;
    scene.add(sofaGroup);

    const tableGroup = new THREE.Group();
    const tableTop = new THREE.Mesh(new THREE.BoxGeometry(2.2, 0.14, 1.2), frameMat);
    tableTop.position.set(0, 0.75, 0);
    tableGroup.add(tableTop);
    [[-0.9, -0.45], [0.9, -0.45], [-0.9, 0.45], [0.9, 0.45]].forEach(([lx, lz]) => {
      const leg = new THREE.Mesh(new THREE.BoxGeometry(0.12, 0.75, 0.12), frameMat);
      leg.position.set(lx, 0.375, lz);
      tableGroup.add(leg);
    });
    tableGroup.position.set(-0.3, 0, 0.2);
    scene.add(tableGroup);

    const rug = new THREE.Mesh(
      new THREE.PlaneGeometry(5.6, 4.2),
      new THREE.MeshStandardMaterial({ color: "indianred", roughness: 0.85 })
    );
    rug.rotation.x = -Math.PI / 2;
    rug.position.set(-1.1, 0.02, -0.8);
    scene.add(rug);

    const bulbMat = new THREE.MeshStandardMaterial({
      color: "gray",
      emissive: "gold",
      emissiveIntensity: 0
    });
    const ceilingLightMesh = new THREE.Mesh(
      new THREE.CylinderGeometry(0.45, 0.45, 0.14, 24),
      bulbMat
    );
    ceilingLightMesh.position.set(-2.2, 4.65, -0.6);
    scene.add(ceilingLightMesh);

    const fanMount = new THREE.Mesh(
      new THREE.CylinderGeometry(0.12, 0.12, 0.85, 12),
      new THREE.MeshStandardMaterial({ color: "silver" })
    );
    fanMount.position.set(1.2, 4.3, 0.4);
    scene.add(fanMount);

    const fanBladesGroup = new THREE.Group();
    fanBladesGroup.position.set(1.2, 3.88, 0.4);
    const bladeMat = new THREE.MeshStandardMaterial({ color: "whitesmoke" });
    for (let i = 0; i < 4; i++) {
      const blade = new THREE.Mesh(new THREE.BoxGeometry(1.75, 0.04, 0.26), bladeMat);
      const angle = (i * Math.PI) / 2;
      blade.position.set(Math.cos(angle) * 0.88, 0, Math.sin(angle) * 0.88);
      blade.rotation.y = -angle;
      fanBladesGroup.add(blade);
    }
    scene.add(fanBladesGroup);

    const tvFrame = new THREE.Mesh(
      new THREE.BoxGeometry(2.6, 1.45, 0.12),
      new THREE.MeshStandardMaterial({ color: "black" })
    );
    tvFrame.position.set(2.2, 2.3, -5.82);
    scene.add(tvFrame);

    const tvScreenMat = new THREE.MeshStandardMaterial({ color: "darkslategray", emissive: "black" });
    const tvScreen = new THREE.Mesh(new THREE.PlaneGeometry(2.38, 1.25), tvScreenMat);
    tvScreen.position.set(2.2, 2.3, -5.75);
    scene.add(tvScreen);

    const acBody = new THREE.Mesh(
      new THREE.BoxGeometry(2.2, 0.65, 0.55),
      new THREE.MeshStandardMaterial({ color: "white" })
    );
    acBody.position.set(-2.2, 3.6, -5.65);
    scene.add(acBody);

    const acStatusLedMat = new THREE.MeshStandardMaterial({ color: "red", emissive: "black" });
    const acLed = new THREE.Mesh(new THREE.SphereGeometry(0.06, 10, 10), acStatusLedMat);
    acLed.position.set(-1.3, 3.55, -5.36);
    scene.add(acLed);

    const controlBoxMesh = new THREE.Mesh(
      new THREE.BoxGeometry(0.32, 1.25, 0.95),
      new THREE.MeshStandardMaterial({ color: "royalblue", metalness: 0.2, roughness: 0.4 })
    );
    controlBoxMesh.position.set(-5.82, 2.0, 0.8);
    scene.add(controlBoxMesh);

    const boxStatusMat = new THREE.MeshStandardMaterial({
      color: "cyan",
      emissive: "deepskyblue",
      emissiveIntensity: 0.7
    });
    const boxStatusStrip = new THREE.Mesh(new THREE.BoxGeometry(0.04, 0.95, 0.65), boxStatusMat);
    boxStatusStrip.position.set(-5.65, 2.0, 0.8);
    scene.add(boxStatusStrip);

    const sensorMesh = new THREE.Mesh(
      new THREE.BoxGeometry(0.2, 0.28, 0.35),
      new THREE.MeshStandardMaterial({ color: "white" })
    );
    sensorMesh.position.set(-5.82, 3.65, 3.2);
    scene.add(sensorMesh);

    function createHighlightedWire(pointsArray, colorName, radius = 0.045) {
      const vecPoints = pointsArray.map(([x, y, z]) => new THREE.Vector3(x, y, z));
      const curve = new THREE.CatmullRomCurve3(vecPoints, false, "catmullrom", 0.05);
      const tubeGeo = new THREE.TubeGeometry(curve, 64, radius, 10, false);
      const tubeMat = new THREE.MeshStandardMaterial({
        color: colorName,
        emissive: colorName,
        emissiveIntensity: 0.25,
        roughness: 0.3
      });
      const mesh = new THREE.Mesh(tubeGeo, tubeMat);
      scene.add(mesh);
      return tubeMat;
    }

    const wireSensorMat = createHighlightedWire(
      [[-5.78, 2.55, 1.05], [-5.78, 3.65, 1.05], [-5.78, 3.65, 3.05]],
      "cyan",
      0.038
    );
    const wireLightMat = createHighlightedWire(
      [[-5.78, 2.6, 0.65], [-5.78, 4.72, 0.65], [-5.78, 4.72, -0.6], [-2.2, 4.72, -0.6]],
      "gold",
      0.045
    );
    const wireFanMat = createHighlightedWire(
      [[-5.78, 2.6, 0.85], [-5.78, 4.76, 0.85], [-5.78, 4.76, 0.4], [1.2, 4.76, 0.4], [1.2, 4.5, 0.4]],
      "lime",
      0.045
    );
    const wireTvMat = createHighlightedWire(
      [[-5.78, 2.4, 0.55], [-5.78, 4.35, 0.55], [-5.78, 4.35, -5.82], [2.2, 4.35, -5.82], [2.2, 2.95, -5.82]],
      "deepskyblue",
      0.045
    );
    const wireAcMat = createHighlightedWire(
      [[-5.78, 2.2, 0.45], [-5.78, 3.95, 0.45], [-5.78, 3.95, -5.82], [-2.2, 3.95, -5.82], [-2.2, 3.85, -5.65]],
      "orangered",
      0.055
    );

    // --- PERSON CHARACTER WITH WHITE BODY/SKIN & BLACK CLOTHES ---
    const personGroup = new THREE.Group();

    const bodyWhiteMat = new THREE.MeshStandardMaterial({ color: "white", roughness: 0.55 });
    const hairBlackMat = new THREE.MeshStandardMaterial({ color: "black", roughness: 0.8 });
    const shirtBlackMat = new THREE.MeshStandardMaterial({ color: "black", roughness: 0.68 });
    const pantsBlackMat = new THREE.MeshStandardMaterial({ color: "black", roughness: 0.75 });
    const shoeBlackMat = new THREE.MeshStandardMaterial({ color: "black", roughness: 0.6 });

    const head = new THREE.Mesh(new THREE.SphereGeometry(0.24, 20, 20), bodyWhiteMat);
    head.position.set(0, 2.34, 0);

    const hairCap = new THREE.Mesh(
      new THREE.SphereGeometry(0.252, 20, 16, 0, Math.PI * 2, 0, Math.PI * 0.58),
      hairBlackMat
    );
    hairCap.position.set(0, 2.36, -0.01);
    hairCap.rotation.x = -0.15;

    const eyeMat = new THREE.MeshStandardMaterial({ color: "black" });
    const leftEye = new THREE.Mesh(new THREE.SphereGeometry(0.028, 10, 10), eyeMat);
    leftEye.position.set(-0.08, 2.36, 0.225);
    const rightEye = new THREE.Mesh(new THREE.SphereGeometry(0.028, 10, 10), eyeMat);
    rightEye.position.set(0.08, 2.36, 0.225);

    const neck = new THREE.Mesh(new THREE.CylinderGeometry(0.09, 0.1, 0.14, 12), bodyWhiteMat);
    neck.position.set(0, 2.06, 0);

    const torso = new THREE.Mesh(new THREE.CylinderGeometry(0.27, 0.24, 0.92, 16), shirtBlackMat);
    torso.position.set(0, 1.56, 0);

    function createArm(sideSign) {
      const shoulderPivot = new THREE.Group();
      shoulderPivot.position.set(sideSign * 0.34, 1.92, 0);

      const upperArm = new THREE.Mesh(new THREE.CylinderGeometry(0.075, 0.068, 0.42, 12), shirtBlackMat);
      upperArm.position.set(0, -0.21, 0);

      const forearm = new THREE.Mesh(new THREE.CylinderGeometry(0.062, 0.054, 0.38, 12), bodyWhiteMat);
      forearm.position.set(0, -0.58, 0.03);
      forearm.rotation.x = -0.15;

      const hand = new THREE.Mesh(new THREE.SphereGeometry(0.072, 12, 12), bodyWhiteMat);
      hand.scale.set(0.85, 1.15, 0.65);
      hand.position.set(0, -0.8, 0.06);

      shoulderPivot.add(upperArm, forearm, hand);
      return shoulderPivot;
    }

    const leftArmPivot = createArm(-1);
    const rightArmPivot = createArm(1);

    function createLeg(sideSign) {
      const hipPivot = new THREE.Group();
      hipPivot.position.set(sideSign * 0.13, 1.08, 0);

      const leg = new THREE.Mesh(new THREE.CylinderGeometry(0.095, 0.078, 0.96, 12), pantsBlackMat);
      leg.position.set(0, -0.48, 0);

      const shoe = new THREE.Mesh(new THREE.BoxGeometry(0.16, 0.11, 0.28), shoeBlackMat);
      shoe.position.set(0, -1.02, 0.05);

      hipPivot.add(leg, shoe);
      return hipPivot;
    }

    const leftLegPivot = createLeg(-1);
    const rightLegPivot = createLeg(1);

    personGroup.add(
      head,
      hairCap,
      leftEye,
      rightEye,
      neck,
      torso,
      leftArmPivot,
      rightArmPivot,
      leftLegPivot,
      rightLegPivot
    );
    personGroup.position.set(-7.8, 0, 3.2);
    scene.add(personGroup);

    function personEntersRoom() {
      if (personInside && personWaypoints.length === 0) return;
      personInside = true;
      offDelayTimer = 0;
      doorTargetAngle = -Math.PI / 2.2;

      personWaypoints = [
        new THREE.Vector3(-6.0, 0, 3.2),
        new THREE.Vector3(-3.2, 0, 2.6),
        new THREE.Vector3(0.6, 0, 1.2)
      ];

      if (mode === "AUTO") {
        onDelayTimer = 1;
        logRelay("Person entered door -> Waiting 1s relay ON-delay before energizing relays...");
      } else {
        logRelay("Person entered door.");
        applyRelayLogic();
      }
      updateDashboard();
    }

    function personExitsRoom() {
      if (!personInside && personWaypoints.length === 0) return;
      personInside = false;
      onDelayTimer = 0;
      doorTargetAngle = -Math.PI / 2.2;

      personWaypoints = [
        new THREE.Vector3(-3.2, 0, 2.6),
        new THREE.Vector3(-6.0, 0, 3.2),
        new THREE.Vector3(-7.8, 0, 3.2)
      ];

      if (mode === "AUTO" && (appliancesOn.light || appliancesOn.fan || appliancesOn.tv || appliancesOn.ac)) {
        offDelayTimer = 5;
        logRelay("Person exited through door -> 5s OFF-delay timer started.");
      } else {
        logRelay("Person exited through door.");
      }
      applyRelayLogic();
      updateDashboard();
    }

    function setControlMode(newMode) {
      mode = newMode;
      document.getElementById("btnAuto").classList.toggle("active", mode === "AUTO");
      document.getElementById("btnForceOn").classList.toggle("active", mode === "FORCE_ON");
      document.getElementById("btnForceOff").classList.toggle("active", mode === "FORCE_OFF");
      onDelayTimer = 0;
      logRelay(`Control mode set to ${mode}.`);
      applyRelayLogic();
      updateDashboard();
    }

    function setRelay(key, turnOn, label) {
      if (appliancesOn[key] !== turnOn) {
        appliancesOn[key] = turnOn;
        logRelay(`${label} ${turnOn ? "CLOSED (ON)" : "OPENED (OFF)"}.`);
      }
    }

    function applyRelayLogic() {
      if (mode === "FORCE_ON") {
        offDelayTimer = 0;
        setRelay("light", true, "Relay CH1 (Ceiling Light)");
        setRelay("fan", true, "Relay CH2 (Ceiling Fan)");
        setRelay("tv", true, "Relay CH3 (TV)");
        if (acCooldownTimer === 0 && !appliancesOn.ac) {
          setRelay("ac", true, "Relay CH4 / 2P Contactor (AC)");
        }
        return;
      }

      if (mode === "FORCE_OFF") {
        offDelayTimer = 0;
        setRelay("light", false, "Relay CH1 (Ceiling Light)");
        setRelay("fan", false, "Relay CH2 (Ceiling Fan)");
        setRelay("tv", false, "Relay CH3 (TV)");
        if (appliancesOn.ac) {
          setRelay("ac", false, "Relay CH4 / 2P Contactor (AC)");
          acCooldownTimer = 6;
          logRelay("AC compressor cooldown (6s) started.");
        }
        return;
      }

      if (personInside) {
        offDelayTimer = 0;
        if (onDelayTimer === 0) {
          setRelay("light", true, "Relay CH1 (Ceiling Light)");
          setRelay("fan", true, "Relay CH2 (Ceiling Fan)");
          setRelay("tv", true, "Relay CH3 (TV)");
          if (acCooldownTimer === 0 && !appliancesOn.ac) {
            setRelay("ac", true, "Relay CH4 / 2P Contactor (AC)");
          }
        }
      } else {
        if (offDelayTimer === 0) {
          setRelay("light", false, "Relay CH1 (Ceiling Light)");
          setRelay("fan", false, "Relay CH2 (Ceiling Fan)");
          setRelay("tv", false, "Relay CH3 (TV)");
          if (appliancesOn.ac) {
            setRelay("ac", false, "Relay CH4 / 2P Contactor (AC)");
            acCooldownTimer = 6;
            logRelay("AC compressor cooldown (6s) started.");
          }
        }
      }
    }

    function updateRelayCard(cardId, pillId, isOn, isCooldown = false, cooldownSec = 0) {
      const card = document.getElementById(cardId);
      const pill = document.getElementById(pillId);
      card.classList.remove("energized", "cooldown");

      if (isOn) {
        card.classList.add("energized");
        pill.textContent = "CLOSED (ON)";
        pill.className = "pill pill-on";
      } else if (isCooldown) {
        card.classList.add("cooldown");
        pill.textContent = `LOCK (${cooldownSec}s)`;
        pill.className = "pill pill-lock";
      } else {
        pill.textContent = "OPEN (OFF)";
        pill.className = "pill pill-off";
      }
    }

    function updateDashboard() {
      const occText = personInside ? "INSIDE ROOM" : "OUTSIDE DOOR (EMPTY)";
      const onText = onDelayTimer > 0 ? `${onDelayTimer}s` : "IDLE";
      const offText = offDelayTimer > 0 ? `${offDelayTimer}s` : "IDLE";
      const acLockText = acCooldownTimer > 0 ? `WAIT ${acCooldownTimer}s` : (appliancesOn.ac ? "RUNNING" : "READY");
      document.getElementById("lcdScreen").innerHTML =
        `MODE: ${mode} | PERSON: ${occText}<br />` +
        `ON-DELAY: ${onText} | OFF-DELAY: ${offText} | AC: ${acLockText}`;

      updateRelayCard("relayCardLight", "pillLight", appliancesOn.light);
      updateRelayCard("relayCardFan", "pillFan", appliancesOn.fan);
      updateRelayCard("relayCardTv", "pillTv", appliancesOn.tv);
      updateRelayCard(
        "relayCardAc",
        "pillAc",
        appliancesOn.ac,
        !appliancesOn.ac && acCooldownTimer > 0,
        acCooldownTimer
      );

      document.getElementById("acRelayNote").textContent =
        !appliancesOn.ac && acCooldownTimer > 0
          ? `1500W AC • Cooldown Lock Active (${acCooldownTimer}s left)`
          : "1500W AC • Red-Orange Heavy Conduit";

      wireSensorMat.emissiveIntensity = personInside ? 1.1 : 0.25;
      wireLightMat.emissiveIntensity = appliancesOn.light ? 1.1 : 0.15;
      wireFanMat.emissiveIntensity = appliancesOn.fan ? 1.1 : 0.15;
      wireTvMat.emissiveIntensity = appliancesOn.tv ? 1.1 : 0.15;
      wireAcMat.emissiveIntensity = appliancesOn.ac ? 1.1 : 0.15;

      mainRoomLight.intensity = appliancesOn.light ? 0.95 : 0;
      bulbMat.emissiveIntensity = appliancesOn.light ? 0.65 : 0;
      ambientLight.intensity = appliancesOn.light ? 0.42 : 0.22;

      tvScreenMat.emissive.set(appliancesOn.tv ? "deepskyblue" : "black");
      tvScreenMat.color.set(appliancesOn.tv ? "skyblue" : "darkslategray");

      acStatusLedMat.emissive.set(appliancesOn.ac ? "lime" : (acCooldownTimer > 0 ? "orange" : "black"));
      acStatusLedMat.color.set(appliancesOn.ac ? "lime" : (acCooldownTimer > 0 ? "orange" : "red"));

      let drawnWatts = 0;
      if (appliancesOn.light) drawnWatts += WATTS.light;
      if (appliancesOn.fan) drawnWatts += WATTS.fan;
      if (appliancesOn.tv) drawnWatts += WATTS.tv;
      if (appliancesOn.ac) drawnWatts += WATTS.ac;

      const totalUncontrolledWatts = WATTS.light + WATTS.fan + WATTS.tv + WATTS.ac;
      document.getElementById("powerDrawnVal").textContent = `${drawnWatts} W`;
      document.getElementById("uncontrolledWattsVal").textContent = `${totalUncontrolledWatts} W`;
      document.getElementById("energySavedWhVal").textContent = `${energySavedWh.toFixed(2)} Wh`;
      document.getElementById("energySavedKwhVal").textContent = `${(energySavedWh / 1000).toFixed(4)} kWh`;
    }

    function resetCounters() {
      energySavedWh = 0;
      logRelay("Electricity saved counter reset to 0.00 Wh.");
      updateDashboard();
    }

    setInterval(() => {
      if (mode === "AUTO" && personInside && onDelayTimer > 0) {
        onDelayTimer--;
        if (onDelayTimer === 0) {
          logRelay("1s entry relay delay complete -> Energizing relays.");
          applyRelayLogic();
        }
      }

      if (mode === "AUTO" && !personInside && offDelayTimer > 0) {
        offDelayTimer--;
        if (offDelayTimer === 0) {
          applyRelayLogic();
        }
      }

      if (acCooldownTimer > 0) {
        acCooldownTimer--;
        if (acCooldownTimer === 0) {
          logRelay("AC compressor cooldown complete.");
          applyRelayLogic();
        }
      }

      const totalUncontrolledWatts = WATTS.light + WATTS.fan + WATTS.tv + WATTS.ac;
      let drawnWatts = 0;
      if (appliancesOn.light) drawnWatts += WATTS.light;
      if (appliancesOn.fan) drawnWatts += WATTS.fan;
      if (appliancesOn.tv) drawnWatts += WATTS.tv;
      if (appliancesOn.ac) drawnWatts += WATTS.ac;

      if (!personInside) {
        const savedWattsNow = Math.max(0, totalUncontrolledWatts - drawnWatts);
        energySavedWh += savedWattsNow / 3600;
      }

      updateDashboard();
    }, 1000);

    function animate() {
      requestAnimationFrame(animate);

      if (personWaypoints.length > 0) {
        const target = personWaypoints[0];
        const dir = new THREE.Vector3().subVectors(target, personGroup.position);
        const dist = dir.length();
        if (dist > 0.08) {
          dir.normalize();
          personGroup.position.addScaledVector(dir, 0.065);
          personGroup.rotation.y = Math.atan2(dir.x, dir.z);

          const stride = Math.sin(Date.now() * 0.015) * 0.45;
          leftLegPivot.rotation.x = stride;
          rightLegPivot.rotation.x = -stride;
          leftArmPivot.rotation.x = -stride * 0.75;
          rightArmPivot.rotation.x = stride * 0.75;
        } else {
          personGroup.position.copy(target);
          personWaypoints.shift();
          if (personWaypoints.length === 0) {
            leftLegPivot.rotation.x = 0;
            rightLegPivot.rotation.x = 0;
            leftArmPivot.rotation.x = 0;
            rightArmPivot.rotation.x = 0;
            doorTargetAngle = 0;
          }
        }
      }

      doorPivot.rotation.y += (doorTargetAngle - doorPivot.rotation.y) * 0.1;

      if (appliancesOn.fan) {
        fanBladesGroup.rotation.y += 0.25;
      }

      renderer.render(scene, camera);
    }

    logRelay("Ready: Click 'Person Enters Door' (1s relay ON-delay active).");
    updateDashboard();
    animate();
  </script>
</body>
</html>
