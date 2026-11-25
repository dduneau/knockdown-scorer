# Knockdown scorer

A browser app that scores heat-knockdown (CTmax) videos of *Drosophila* in multi-well arenas.
It tracks the fly in every well, finds when each one stops walking for good (drops), and reads the
plate temperature from the video overlay (`Temp: xx.xx C`) at that exact frame.

Everything runs in the browser: the video is opened from your computer and never uploaded.

## Use it
1. Open the page and drop in the video (MP4/H.264 or WebM).
2. Drag the two dots onto the centres of the first and last wells (A1 and D6); untick empty wells by clicking them.
   Rings show which wells have a detected fly. Make sure the yellow dashed box covers the temperature text.
3. Optionally label the wells under *Genotype, treatment and sex*: paint groups onto the plate map, or load a CSV
   with one row per well (`well,genotype,treatment,sex`; see `example_layout.csv`). *Download layout CSV* saves the current layout.
4. Press **Analyse video** and keep the tab in front (it pauses in the background).
5. Review the wells marked with a dot; one click accepts a suggested time or sets the knockdown on the current frame.
6. Export with **Download CSV** (results, groups, activity, per-fly activity) or **Copy table**.

The **Activity** tab shows walking per group: a heatmap, the mean of flies still standing against temperature,
activity aligned to each fly's knockdown, and one value per fly (mean activity in a temperature window, peak activity,
temperature at the peak). Activity can be shown on a linear or log2(1 + x) scale.

**Combine videos** (button at the top) pools flies from several videos. After analysing a video, press
**Add to combined set**; the set is kept in your browser until you remove it. You can also load the CSV files the app
exported (knockdown, per-fly activity, activity), from any computer; each export has a `video` column, and older
exports without it are named from the file name. The combined view draws knockdown temperature by group, % still
standing, per-fly activity and activity against temperature, with *Video* available as a grouping, and exports one
combined table. Use the same temperature window in every video before comparing per-fly activity.

The version shown next to the title (and in the browser tab) tells you which build GitHub Pages is serving.

Chrome or Edge recommended (Safari 15.4+ and recent Firefox should also work).

## Host it on GitHub Pages
1. Create a new repository and upload the contents of this folder (keep the `tess/` folder structure).
2. In the repository: **Settings → Pages → Build and deployment → Source: Deploy from a branch**, choose `main` and `/ (root)`, then Save.
3. After a minute the app is live at `https://<your-user>.github.io/<repo-name>/`.

It also works from any static web server (it cannot run from a `file://` double-click,
because the text reader runs in a web worker). To try it locally: `python3 -m http.server` in this folder, then open http://localhost:8000.

## How knockdown is called
- An "empty plate" image is built from frames spread across the video; each fly is the dark blob that differs from it.
- Knockdown = start of the final stillness: the last time the body moved more than half a body length; it must stay still
  for at least 60 s until the end (settings are under *Detection settings*). Leg movements are ignored.
- If a still fly's appearance changes later (it was standing, then toppled), the well is flagged with that time,
  with thumbnails at the call and at the change; you can accept it with one click, or set it to be used automatically.
- Temperature is read with Tesseract OCR from the overlay at the call frame and checked against the ramp read every 10 s.

## Files
- `index.html` — the whole app (HTML, CSS, JavaScript).
- `tess/` — [Tesseract.js](https://github.com/naptha/tesseract.js) 7.0.0 (Apache-2.0) and its English model
  (stored as base64 text in `tess/lang/eng.traineddata.b64.txt`). Licences are in `tess/`.
