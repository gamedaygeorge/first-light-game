# FIRST LIGHT — Earth Observation Crisis Management Game

## Project Goal

Build a single HTML file game for a LinkedIn portfolio post by George Mathieson,
founder of Diamond Sky (diamondsky.au), an Earth observation data company.

The game should feel like a real mission control system — professional, data-driven,
and grounded in how EO satellites actually work. Terminology, satellite types, and
data products should reflect real EO practice.

## EO Background (for flavour text, UI labels, and subagent context)

### Orbit Types

- **LEO (Low Earth Orbit)** — 400–2000km altitude. High resolution, short revisit
  windows (minutes per pass). Most imaging satellites live here.
- **GEO (Geostationary Orbit)** — 36,000km. Fixed position over one region.
  Continuous coverage but lower resolution. Weather satellites (e.g. Himawari, GOES).
- **MEO (Medium Earth Orbit)** — between LEO and GEO. Used for navigation (GPS, Galileo).
- **SSO (Sun-Synchronous Orbit)** — a LEO orbit that always crosses the equator at the
  same local solar time. Most optical imaging satellites use SSO for consistent lighting.

### Sensor Types

- **Optical (multispectral)** — captures visible + near-infrared. Weather-dependent
  (blocked by cloud). Used for land cover, vegetation, urban mapping.
- **Optical (hyperspectral)** — many narrow spectral bands. Can detect specific
  chemicals, minerals, gas concentrations.
- **SAR (Synthetic Aperture Radar)** — active sensor, works day/night through cloud.
  Used for flood mapping, subsidence, ship detection, ice monitoring.
- **Thermal infrared** — detects heat signatures. Used for fire detection, urban heat,
  sea surface temperature.
- **Sounder** — measures atmospheric profiles (temperature, humidity, gas
  concentrations) at different altitudes. Used on GEO weather satellites.
- **LiDAR** — laser pulses measure precise elevation. Used for vegetation height,
  ice sheet thickness, coastal mapping.
- **GNSS-RO (Radio Occultation)** — uses GPS signal bending through atmosphere to
  measure temperature and humidity profiles.

### Key EO Concepts

- **Revisit time** — how often a satellite can image the same location
- **Ground resolution** — size of the smallest detectable feature
- **Swath width** — width of the strip imaged on each pass
- **Tasking window** — the time slot when a satellite can be commanded to image a target
- **Data product** — processed output from raw satellite data (e.g. flood extent map,
  fire radiative power, NDVI vegetation index)
- **Ground truth** — field observations used to validate satellite data
- **Overpass** — moment when a satellite passes over a target location
- **First light** — the first image captured by a newly launched satellite

## Satellite Constellation (Player Assets)

Each satellite has an orbit type, sensor, revisit time, and cloud/weather dependency:

| Name         | Orbit             | Sensor                                         | Revisit    | Cloud-free?                      | Best for                                                                                                                                            |
| ------------ | ----------------- | ---------------------------------------------- | ---------- | -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| FIRE-WATCH   | LEO/SSO           | Thermal IR                                     | 12h        | No                               | Wildfires, volcanic heat, urban temperature                                                                                                         |
| FLOOD-EYE    | LEO/SSO           | SAR                                            | 6h         | Yes                              | Flooding, subsidence, ship detection                                                                                                                |
| CARBON-SENSE | LEO/SSO           | Hyperspectral sounder                          | 16h        | Partial                          | GHG plumes, methane, CO2 flux                                                                                                                       |
| URBAN-LENS   | LEO/SSO           | Optical multispectral (0.5m)                   | 24h        | No                               | Deforestation, urban change, crop stress                                                                                                            |
| COAST-GUARD  | LEO/SSO           | Optical multispectral + thermal                | 12h        | No                               | Ocean colour, coastal change, oil spills                                                                                                            |
| STORM-EYE    | GEO               | Sounder + optical                              | Continuous | Partial                          | Tropical storms, weather systems, dust                                                                                                              |
| TOPO-SCAN    | LEO               | LiDAR                                          | 48h        | Yes                              | Elevation change, ice, vegetation height                                                                                                            |
| FOREST-WATCH | LEO/SSO           | Dual payload — optical + SAR                   | 12h        | Partial                          | Deforestation, illegal logging, fire encroachment, biomass change. Auto-selects best sensor based on cloud cover; player can override to force SAR. |
| SOL-WATCH    | L1 Lagrange point | Solar wind monitor / magnetometer / EUV imager | Continuous | N/A — observes the Sun not Earth | Solar flares, CME detection, geomagnetic storm warning. Shown as fixed marker sunward, not an orbiting icon.                                        |

## Event Types

Events have a type (crisis or observation), a timescale, urgency, and matched satellites.
Using the wrong satellite returns degraded or no data. Urgency timers scale by event type.

### Crisis Events — Short Term (minutes to hours in-game)

- **Wildfire** — FIRE-WATCH — thermal hotspot + fire radiative power map → aerial suppression / evacuation / controlled burn
- **Flooding** — FLOOD-EYE — inundation extent map → emergency services / flood barrier / population warning
- **Tropical Storm** — STORM-EYE + FLOOD-EYE — storm track + intensity → evacuation order / shelter-in-place / maritime warning
- **Volcanic Eruption** — FIRE-WATCH + CARBON-SENSE — ash plume + SO2 map → no-fly zone / evacuation / aviation alert
- **River Outflow Water Quality Event** — COAST-GUARD + FLOOD-EYE — turbidity/sediment plume → fisheries closure / downstream warning / remediation
- **Oil Spill** — COAST-GUARD — slick extent map → maritime containment / wildlife response / regulatory alert
- **Algal Bloom** — COAST-GUARD — chlorophyll concentration map → water supply warning / fisheries closure / aeration deployment

### Crisis Events — Medium Term (days to weeks in-game)

- **Deforestation Event** — FOREST-WATCH + URBAN-LENS — change detection / NDVI loss → regulatory enforcement / reforestation order / indigenous land alert
- **Illegal Logging** — FOREST-WATCH — SAR backscatter change + optical canopy loss → law enforcement tasking / logging moratorium / satellite monitoring order
- **Fire Encroachment** — FOREST-WATCH + FIRE-WATCH — thermal + canopy damage → firebreak establishment / community evacuation / aerial suppression
- **Biomass Change** — FOREST-WATCH + TOPO-SCAN — canopy height change + biomass index → carbon credit audit / conservation alert / research tasking
- **Crop Failure** — URBAN-LENS + CARBON-SENSE — vegetation stress index (NDVI anomaly) → food security alert / aid logistics / insurance assessment
- **Coastal Erosion** — COAST-GUARD + TOPO-SCAN — shoreline change + elevation loss → infrastructure warning / managed retreat / engineering response
- **Urban Heat Island Escalation** — FIRE-WATCH + URBAN-LENS — land surface temperature anomaly → cooling centre activation / urban greening / building code review
- **Dust Storm** — STORM-EYE + CARBON-SENSE — aerosol optical depth map → aviation warning / health alert / agricultural advisory

### Crisis Events — Long Term (weeks to months in-game, score degrades slowly)

- **Ocean Carbon Flux Anomaly** — CARBON-SENSE — pCO2 partial pressure map → research flag / emissions trading alert / international report
- **Methane Plume (industrial)** — CARBON-SENSE — CH4 column concentration → regulator alert / shutdown order / media disclosure
- **Permafrost Thaw Signal** — CARBON-SENSE + FLOOD-EYE (InSAR mode) — subsidence + emissions map → infrastructure warning / climate report / research tasking
- **Groundwater Depletion** — FLOOD-EYE (InSAR mode) — surface subsidence interferogram → irrigation restriction / aquifer recharge / agricultural alert
- **Ice Sheet Mass Loss** — TOPO-SCAN + FLOOD-EYE — elevation change + velocity map → sea level advisory / climate report / international alert
- **Coral Bleaching** — COAST-GUARD — sea surface temperature + ocean colour → marine park closure / fishing ban / reef restoration

### Space Weather Events (unique category — threat comes from above, not on Earth)

These are the only events where SOL-WATCH is the primary asset. Severe events have
knock-on effects on the rest of the constellation — a major CME temporarily degrades
all LEO satellites, forcing the player to rely on GEO and SOL-WATCH assets only.

- **Solar Flare (X-class)** — SOL-WATCH — EUV + X-ray flux map → satellite safe mode command / HF radio blackout warning / astronaut shelter alert
- **Coronal Mass Ejection (CME)** — SOL-WATCH — solar wind density + velocity plot → power grid pre-emptive load shedding / pipeline current suppression / transformer protection / GPS degradation warning
- **Geomagnetic Storm** — SOL-WATCH + any LEO satellite (showing sensor noise) — Kp index display → grid operator alert / satellite drag compensation / aurora observation bonus
- **Satellite Drag Event** — SOL-WATCH — atmospheric density anomaly at LEO altitudes → constellation orbit adjustment. Gameplay consequence: temporarily increases revisit time on all LEO satellites while they recover, degrading overall constellation capacity.
- **Radio Blackout** — SOL-WATCH — solar proton event → HF communications blackout warning / reroute aviation / emergency services advisory

### Observation Events — Light Touch (bonus objectives, no penalty for missing)

These spawn occasionally. Tasking earns bonus points. Flavour text should be warm and
human — a moment of levity in the mission control aesthetic.

- **Music Festival** (Burning Man / Glastonbury / Coachella) — URBAN-LENS — optical image shows temporary city in desert or field of stages. _"Temporary urban footprint detected. Crowd density nominal. Portable sanitation infrastructure visible at 0.5m resolution. No intervention required."_
- **Whale Migration** — COAST-GUARD — ocean colour + thermal anomaly near coastline. _"Biological hotspot confirmed. Whale aggregation detected. Recommend maritime exclusion zone. Magnificent."_
- **Aurora Event** — SOL-WATCH + any LEO satellite — sensor noise + atmospheric glow. _"Geomagnetic storm affecting sensor calibration. Kp index elevated. Ground operators are reportedly standing outside looking up. Can confirm: worth it."_
- **Stadium Event** — URBAN-LENS — thermal + optical signature of packed stadium. _"Large gathering detected. Heat signature consistent with approximately 90,000 humans enjoying themselves. Vendor activity elevated near northern concourse."_
- **Rocket Launch Plume** — CARBON-SENSE + FIRE-WATCH — thermal + exhaust chemical signature. _"Launch event detected. Propellant plume consistent with hydrocarbon first stage. Ironic that we are using satellites to observe someone launching a satellite."_
- **Bioluminescent Bay** — COAST-GUARD (night pass) — glowing coastal anomaly. _"Dinoflagellate bloom confirmed. Classification: spectacular. Threat level: zero."_
- **Desert Blooming Event** — URBAN-LENS — sudden NDVI spike in arid region after rainfall. _"Vegetation anomaly detected. Wildflower superbloom confirmed. Recommend no action except appreciation."_
- **Antarctic Research Station Resupply** — TOPO-SCAN + URBAN-LENS — vessel + vehicle tracks in snow. _"Resupply operation confirmed. Ice runway in use. Someone down there is about to receive fresh vegetables for the first time in four months."_
- **Satellite Launch Observation** — SOL-WATCH + FIRE-WATCH — launch plume + initial orbital insertion. _"New arrival detected entering LEO. Welcome to the neighbourhood."_

## Game Loop

1. Crisis or observation event spawns on map with urgency timer (scaled by event type)
2. Player selects event, reviews available satellites (some may be on the wrong side of
   Earth, or blocked by cloud — show this clearly)
3. Player tasks a satellite — acquisition delay simulates real tasking window (5–20 seconds
   scaled by orbit type: GEO is near-instant, LEO depends on next overpass)
4. Data product panel renders with simulated false-colour imagery (procedural Canvas)
   and key metrics (extent, intensity, confidence level)
5. Player selects mitigation action (or observation acknowledgement for bonus events)
6. Event resolves — score affected by: correct satellite choice, speed of response,
   mitigation effectiveness, whether cloud/SAR tradeoff was handled correctly
7. New events spawn, difficulty and simultaneity escalate over time
8. Game ends on score threshold or too many unresolved crises

## Scoring

- Correct satellite for sensor type: +points
- Fast response: time bonus
- Wrong satellite (cloud-blocked optical on a cloudy crisis): degraded data, partial score
- Unresolved crisis escalation: score penalty per tick
- Observation bonus events: flat bonus, no penalty for missing
- Streak bonus for resolving consecutive crises correctly

## Visual Style

- Stylised 2D world map, flat/Mercator projection, dark background, glowing coastlines
- Mission control aesthetic: dark UI, green/amber/red data panels, monospace fonts
- Satellite constellation shown as small orbital path arcs above the map
- Crisis events: pulsing alert markers, colour-coded by urgency (red/amber/green)
- Cloud layer: semi-transparent overlay on affected regions
- Data product panel: simulated false-colour imagery (procedural Canvas), key metrics,
  confidence score
- Observation events: softer blue/teal pulse rather than crisis red

## Subagent Strategy

Spin up subagents as you see fit. Suggested roles:

- **Designer** — finalise mechanics, satellite/event matrix, scoring, difficulty curve
- **Builder** — implement HTML/CSS/JS (single file, no external dependencies)
- **Reviewer** — test all event types end to end, check satellite logic, polish UI and
  flavour text

## Constraints

- Output: one file — `first-light.html`
- No external dependencies — all rendering via Canvas and/or SVG, procedural assets
- Must run by opening the file in any modern browser
- No build step, no npm, no frameworks
- Target: visually impressive and thematically credible enough to post on LinkedIn
  as a Diamond Sky portfolio piece

## Done When

- World map renders with stylised continents and cloud layer
- At least 5 crisis types spawn and escalate with correct satellite matching
- All 7 satellites are taskable with acquisition delay and orbit-appropriate timing
- Simulated data products display per event type
- Observation bonus events appear occasionally with flavour text
- Mitigation actions resolve crises with scoring
- Game over / score screen exists
- Zero console errors on load
