# PhenoKnee

A calculator for checking that the bony resections planned and the ones executed agree, in knee arthroplasty.

Enter the leg parameters and it works out the resection depth for each cut under each alignment strategy. Enter what was measured with the caliper in theatre and it shows where the two diverge, and by how much.

**Not a medical device. Not for clinical decision-making.** It computes geometry from figures the user provides. It does not measure the knee, choose a strategy, or judge whether a plan suits a patient.

---

## Contents

```
index.html      the entire application — no build step, no dependencies
netlify.toml    security headers and deploy config
_headers        fallback headers for hosts other than Netlify
DEPLOY.md       deployment instructions
```

## Running it

Open `index.html` in a browser. That is all — there is nothing to install or compile.

## Deploying

See `DEPLOY.md`. Netlify reads `netlify.toml` and needs no build command; the publish directory is the repository root.

## Architecture

Single self-contained HTML file. No framework, no external requests, no server, no database.

Case data lives in browser storage and in the JSON file the user downloads. Nothing about a patient reaches any server. Radiographs opened in the measurement module are read into memory and discarded on reload — never uploaded, never stored.

The Content-Security-Policy in `netlify.toml` sets `connect-src 'none'`, which makes the browser enforce that rather than leaving it to trust. Do not relax it without understanding what is being given up.

## Implant data

Geometry for the MicroPort Evolution medial-pivot knee is taken from the published surgical technique: distal condyle 9 mm, posterior condyle 10 mm in sizes 1–4 and 11 mm in sizes 5–8, 1.27 mm saw blade, inserts 10/12/14/17/20/24 mm. **Verify against the current technique guide before clinical use.**

## Known limitations

- The measurement span constants scale every output and are stated in the app's Method panel. Review them against your own measurement habit.
- The sign convention is internally consistent but has not been validated against films with known PACS values. Do that before relying on any number.
- The implant overlay is present but disabled pending verification of the component outlines.
- Lithuanian strings need a native review.

## Credit

Created by Saulius Brazauskas, 2026.
