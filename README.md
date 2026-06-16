# 🌍 OpenWorld

Spin a 3D globe, click **anywhere on Earth**, and drive a car through that
location's real streets in third person — all from free, open map data with
**no API keys**.

![OpenWorld demo — globe to driving on real streets](assets/demo.gif)

## What it does

1. **Globe** — an interactive 3D Earth (MapLibre globe projection). Drag to spin,
   scroll to zoom, and click any point to drop in. Or hit **Drive from my
   location** to start from where you actually are.
2. **Drill in** — the camera dives down into the real geography of wherever you
   clicked, rendered with stylized 3D buildings from OpenStreetMap.
3. **Drive** — a third-person 3D car you steer with the keyboard, **locked to the
   real road network** (any street, big or small — but no driving through
   buildings), with a procedural manual-gearbox engine sound.

## Controls

| Key | Action |
| --- | --- |
| `W` / `↑` | Accelerate (revs up to ~100 km/h) |
| `S` / `↓` | Brake / reverse |
| `A` `D` / `← →` | Steer |
| 🔊 | Toggle engine sound |

## Tech

- **React + Vite + TypeScript**
- **[MapLibre GL JS](https://maplibre.org)** — globe projection + 3D building
  extrusions, all client-side.
- **[OpenFreeMap](https://openfreemap.org)** — free, key-less vector tiles
  (OpenStreetMap data).
- **[three.js](https://threejs.org)** — the car, rendered through a MapLibre
  *custom layer* so it shares the map's WebGL camera and stays locked to
  real-world coordinates while the map draws the world beneath it.
- **Web Audio** — procedural engine sound (no audio files).

### How it works

- **The car on the map** — the car isn't a separate 3D scene floating over the
  map. [`AvatarLayer`](src/lib/avatarLayer.ts) is a MapLibre custom layer: each
  frame it reads the car's live `lng/lat/heading` from the
  [`BikeController`](src/lib/BikeController.ts), builds the mercator model matrix,
  and renders the three.js car into MapLibre's own camera. The controller runs
  the physics loop and drives the camera to follow behind the car.
- **Staying on the road** — [`RoadNetwork`](src/lib/roads.ts) pulls drivable road
  segments straight from the loaded vector tiles (`transportation` layer). Every
  frame a proposed move is only committed if it stays within the nearest road's
  corridor, and the car snaps onto the nearest road when it spawns.
- **Engine sound** — [`engineSound.ts`](src/lib/engineSound.ts) synthesises a
  looped buffer of exhaust firing pulses and revs it by changing playback speed,
  with a manual-gearbox model so the pitch climbs within a gear and dips on each
  upshift.

## Run it

```bash
npm install
npm run dev      # dev server (Vite)
npm run build    # production bundle
```

## Ideas / next steps

- Route the car along the road *graph* (turn-by-turn) rather than a free-drive
  corridor.
- A real recorded engine sample, pitch-shifted by RPM, for extra realism.
- Mini-map, photo mode, day/night lighting.
