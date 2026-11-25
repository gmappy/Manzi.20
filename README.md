# AETHERIA

A client-side 3D virtual art gallery (`index.html`) with a curator interface (`admin.html`). No backend, no build step.

## Run locally
```
python3 -m http.server 8000
```
Open http://localhost:8000 (gallery) and http://localhost:8000/admin.html (curator).

## Controls
- Move: WASD / arrow keys, on-screen pad on touch. Look: drag. Click the floor to walk, click an artwork to go to it.
- Click the minimap to travel. The **Sky** slider previews other times of day; **Live** returns to your local time.
- Guided Tour: Previous / Play / Next. The map and tour bar can be hidden with their toggle buttons (remembered per browser).
- Music: a generated ambient soundtrack starts on the visitor's first tap or key press (browsers block autoplay). The **Music** button turns it off and on; no audio files are needed.

## Curating and publishing
Visitors see `exhibition.json` (shipped with the site). The admin edits a private draft in your browser; it never changes what visitors see on its own.
1. Open `admin.html`, curate, and check it with **Preview draft**.
2. Click **Publish: download exhibition.json**, replace `exhibition.json` in the project, and redeploy.

**Password:** `admin-config.js` is intentionally shipped with an empty hash. On first open, `admin.html` asks you to create a curator password and shows one line to paste into `admin-config.js`; replace the empty line with that generated hash before deploying so it works on every device. This gate is client-side (a salted PBKDF2 hash): it keeps people out of the curator UI but is not server security. Use your host's access control on `/admin.html` for hard protection. Forgot it? Clear `admin-config.js` and site storage to set a new one.

The admin is not linked from the gallery. Exposing it is harmless: a visitor's edits only affect their own browser. Protect it with your host's password feature or leave it out of the deploy if you prefer.

Draft details: everything is saved to `localStorage` under `aetheria_exhibition_v1` in the visitor's browser. Use the admin to add or edit works, assign one of 16 wall slots, and set tour order (0 = not on the tour). Export JSON to back up or move to another device, then Import there. Reset restores the sample show. Uploaded images are downscaled to about 900px JPEG to fit browser storage.

## 3D art
Set an item's **Type** to *3D model* in the admin. It stands on one of 9 pedestals (central hall, or near/far in each gallery room), turns slowly, and joins the tour like any other work.
- **Built-in shapes** (knot, crystal, spiral, orb, tower) with a colour need no files.
- **Your own `.glb`**: paste a URL, or upload a file (draft limit ~2.5 MB). On **Publish**, uploaded models download as `<id>.glb`; put them in a `models/` folder next to `index.html` and redeploy. Keep models small (a few MB) and use self-contained binary glTF (`.glb`).
- If a model fails to load (or the loader script is blocked), the gallery shows a grey orb instead of breaking.

## Deploy
- **Vercel**: import the repo, framework "Other", no build command. `vercel.json` adds `/admin`.
- **Netlify**: `netlify.toml` publishes the repo root. For a `public` publish dir, copy `index.html`, `admin.html`, `store.js` into it; `public/_redirects` handles `/admin`.
- Any static host works (GitHub Pages, S3, Cloudflare Pages).

Three.js r128 loads from cdnjs. If it or WebGL is unavailable, the gallery falls back to a 2D list of the exhibition. Curation is per-browser; exhibitions are shared through exported JSON.


## Realistic architecture and performance update
The central salon now uses a procedural polished-stone finish with brass inlay; the four wings retain warm oak flooring and textured plaster. The glass roof lantern, bronze mullions, recessed ceiling fixtures, framed wall bays and warm sconces add architectural detail. ACES filmic tone mapping and procedural surface maps remain in place.

Mobile devices use a lower rendering pixel ratio, disable expensive real-time shadows, and refresh the minimap less often. Rendering pauses while the page is hidden. Desktop retains soft shadows. The original floor plan, navigation, artwork placement, curator workflow, exhibition data, and guided tour remain intact.

The supplied 27.9 MB GLB has been inspected: its bounds are approximately 31.9 × 17.5 × 31.9 units. It is not inserted as the building shell because its geometry and vertical scale do not match the existing walkable floor plan. The gallery continues to load individual artwork GLBs through its existing curator workflow. A proper shell replacement requires a deliberate coordinate and collision/navigation remap, not simply dropping the model into the scene.

## Release notes — premium build
- The gallery runs as a static site: no server runtime or build command is required.
- Artwork without an image now receives generated artwork safely (including newly-created curator entries).
- Curator preview rebuilds dispose artwork GPU resources to reduce memory growth during repeated edits.
- The supplied 27.9 MB `vr_gallery_for_product_showcase_2020.glb` is a separate room/showcase asset, not a drop-in replacement for this gallery's four-wing floor plan. It is intentionally not wired in as the building shell: doing so without remapping the floor plan, scale, doors, and collision system would break navigation. Individual artwork GLBs remain supported through the curator workflow.
- Before publishing, configure the curator password and protect `/admin.html` with host-level access control if the admin area must be private.
- Verify the deployed URL on an Android phone and a desktop browser. WebGL and the Three.js CDN are required for the 3D view; a 2D exhibition list is shown if WebGL cannot start.
