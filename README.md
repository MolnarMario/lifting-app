<h1 align="center">Ironlog</h1>

<p align="center">
  <b>A powerlifting log, a plate calculator and a food tracker in one.</b><br>
  Log every set, see which plates go on the bar, track calories and macros,
  and watch both on one calendar. No account, and your data stays in your
  browser.
</p>

<p align="center">
  <a href="https://ironlog.thebarbellengineer.com/"><b>Open the app</b></a> ·
  <a href="https://thebarbellengineer.com/projects/ironlog/">Project page</a> ·
  <a href="#get-started">Get started</a> ·
  <a href="LICENSE">MIT license</a>
</p>

<p align="center">
  <img src="https://thebarbellengineer.com/assets/img/ironlog-1600.webp" alt="Ironlog's workout log, plate loader and food tracker on three phones" width="720">
</p>

## Why

I train powerlifting, and the apps I tried either wanted an account, buried
the log under social features, or kept training and food in two places that
never talked to each other. Ironlog is the tool I use for my own training:
quick to log at the rack, a plate loader for the maths between sets, and
food on the same calendar, so a bad session can be read next to the week of
eating that led to it.

## What it does

### Log the session

- **Set by set**, with weight, reps and either RPE or a percentage of your
  max, per-set gear, and notes.
- **Readiness meters** for sleep, motivation, hydration, calories, protein
  and stimulants, so you can see how you came in.
- **Session timing**, automatic or entered by hand.
- **A 47-lift exercise library**, with your own exercises added alongside.

### Load the bar

- **The plate loader draws the barbell** and works out the plates per side,
  with bar and collar options and kg micro plates.
- **One tap from the log.** The plate icon on any logged set sends that
  weight straight to the loader.

### Track food

- **Calories and macros against daily targets**, with water and bodyweight.
- **A 92-food pantry** plus your own foods, in grams or ounces, millilitres
  or fluid ounces.
- **Scan a label.** Photograph the back of a package and the app finds the
  nutrition table and reads it, on your device. A barcode, when it has one,
  is looked up on Open Food Facts first.

### See the trend

- **Progress:** estimated one-rep max and top set per lift, recent records,
  weekly volume, weekly sets per muscle group, your week streak, and
  bodyweight against volume.
- **Food trends:** average calories, protein and water, macro composition and
  bodyweight over time.
- **One calendar for both.** The month view can overlay each day's calories,
  macros, water and bodyweight on the training grid.

### Your data, your units

- **Switch kg and lb freely.** Weights are stored in kilograms and converted
  for display, so changing units never rewrites your history.
- **Stays in your browser.** No account and no server. If the browser refuses
  to save (private mode, full storage), a banner says so instead of failing
  silently.

## Get started

1. Open **https://ironlog.thebarbellengineer.com/** on your phone or
   computer. On a phone, add it to your home screen to use it like an app.
2. Set your one-rep maxes for squat, bench and deadlift, so percentage-based
   sets work out their weights.
3. On **Workout > Today**, pick an exercise and log your sets.
4. Between sets, tap the plate icon on a set to see the bar in **Plates**.
5. Log meals under **Food**, set your daily targets under **Goals**, and turn
   on **+ Food** in the workout calendar to see both together.

Your data lives in this browser only. Using another browser or device, or
clearing site data, starts empty.

## Run it locally

Needs Node.js 22.6 or newer (the tests run TypeScript directly).

```sh
npm install
npm run dev       # dev server
npm run build     # typecheck, then build into docs/
npm run preview   # serve the built output
npm test          # plates, food, stats, label, OCR, panel, barcode and import tests
```

The label scanner loads its OCR engine (Tesseract, about 6 MB) from jsDelivr
the first time someone scans. To ship it with the build instead, run
`npm run fetch-ocr-assets` and set `LOCAL_ASSETS` to true in
`src/lib/ocr.ts`.

**Deploying:** `npm run build` writes to `docs/`, which is committed, and
GitHub Pages serves **main → /docs**. The root `index.html` is the Vite
entry, not the published page.

## How it works

- **React, TypeScript and Vite**, with no backend. `src/hooks/useLocalStorage.ts`
  keeps one shared store per key, so every component reading a key sees the
  same value.
- **The maths is pure and tested.** Plates, food, statistics, label parsing,
  OCR geometry, finding the table in a photo and barcode handling each live in
  `src/lib/` with no React or DOM, so they run under plain Node.
- **The plate solver works in the displayed unit.** In lb mode the bar really
  is 45 lb and the plates are 45s, 35s and 25s, not converted kilos. It solves
  in thousandths so 0.25 kg plates don't drift.
- **Scanning a whole photo** first reads the frame cheaply only to find where
  the nutrition table is, then reads that crop at a size the OCR engine can
  handle. Romanian and English labels both parse, and per-serving values are
  rescaled to per 100 g.

### Coming from the original build

Ironlog started as a single 424 KB HTML file. That build is kept at
[`legacy/index.html`](legacy/index.html) as the reference for how everything
should behave. On first load, if data from it is on the device, a banner
offers to import it. Exercises are matched by name (an unknown name becomes a
custom exercise rather than losing its sets), weights convert to kilograms,
and the food journal comes across. The banner reports what came over and
names anything with no place in the new build.

The import runs once. The three keys both builds share are copied to
`ironlog-legacy-backup.v1` first, a name that "Clear saved data" doesn't
touch, and the old food journal is never modified.

## License

[MIT](LICENSE). Built by Mario Molnar.
