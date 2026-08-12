# PhenoKnee — deployment

Three files, 148 KB, no build step, no dependencies, no server. Route A takes about two minutes and needs no account beyond Netlify. Route B adds version history and automatic redeploys.

```
index.html      the entire app
netlify.toml    security headers and build config
_headers        fallback headers for hosts other than Netlify
```

---

## Route A — drag and drop (fastest)

Use this to get a working link today.

1. Put the three files in a folder called `phenoknee`. `index.html` must be at the top level, not inside a subfolder.
2. Go to **app.netlify.com** and sign up (GitHub, GitLab or email — all free).
3. On the **Sites** page, find the drop zone that says *Drag and drop your site output folder here*.
4. Drag the whole `phenoknee` folder onto it. Not the individual files — the folder.
5. Wait about twenty seconds. You get a URL like `spontaneous-marzipan-8a3f2c.netlify.app`.
6. **Site configuration → Site details → Change site name** → set it to `phenoknee`. Your link becomes `phenoknee.netlify.app`.

Done. HTTPS is automatic and included.

The limitation: to publish a change you drag the folder again. There is no history and no rollback. Fine for showing a surgeon this week; not what you want once people rely on it.

---

## Route B — GitHub connected (recommended)

Every push redeploys automatically, and you can roll back to any previous version from the Netlify dashboard.

### One-time setup

1. **Create the repository.** On github.com: **New repository**, name it `phenoknee`, set it **Private**, do not add a README. Private is fine — Netlify deploys private repos on the free tier. (GitHub Pages does not, which is one reason to prefer Netlify here.)

2. **Get the files into it.** The web route avoids installing anything: on the empty repo page click **uploading an existing file**, drag all three files in, and commit.

   If you prefer the command line and have Git installed:
   ```
   cd phenoknee
   git init
   git add .
   git commit -m "PhenoKnee initial"
   git branch -M main
   git remote add origin https://github.com/YOUR-USERNAME/phenoknee.git
   git push -u origin main
   ```

3. **Connect Netlify.** app.netlify.com → **Add new site → Import an existing project → Deploy with GitHub** → authorise → pick `phenoknee`.

4. **Build settings.** Netlify will read `netlify.toml` and fill these in. Confirm they read:
   ```
   Branch to deploy:   main
   Build command:      (empty)
   Publish directory:  .
   ```
   If it has guessed a framework and put something in the build command, clear it. There is no build step.

5. **Deploy site.** About thirty seconds.

6. **Rename it.** Site configuration → Site details → Change site name → `phenoknee`.

### Publishing a change afterwards

Edit the file, commit, push. Netlify rebuilds within a minute. On the **Deploys** tab you can preview any past deploy and click **Publish deploy** to roll back instantly.

---

## Optional — your own domain

About €12 a year. Buy `phenoknee.lt` (or `.com`) from any registrar, then in Netlify: **Domain management → Add a domain**, enter it, and follow the DNS instructions. Netlify issues the TLS certificate automatically. Allow up to an hour for DNS to propagate.

---

## What the config does, and why not to loosen it

`netlify.toml` sets `Content-Security-Policy: … connect-src 'none' …`. That is the important line. It tells the browser to **block every outbound network request the page attempts**.

This is what turns "radiographs never leave your device" from a promise in the copy into something the browser enforces. If anyone later adds an analytics snippet, a font from a CDN, or an upload, it will fail loudly rather than quietly sending patient images somewhere.

The rest: `X-Frame-Options: DENY` and `frame-ancestors 'none'` stop the app being embedded in someone else's page; `Referrer-Policy: no-referrer` stops your URL leaking when a surgeon clicks through to a journal; `Permissions-Policy` switches off camera, microphone and location, none of which the app uses.

`Cache-Control: must-revalidate` on `index.html` means surgeons get the current version on every visit rather than a stale cached one — important when you are still correcting things.

---

## Verify before showing anyone

Open the live URL and check:

- [ ] Loads on a real iPhone and a real Android phone, not just a desktop browser
- [ ] All five tabs reachable; nav does not clip on a narrow screen
- [ ] LDFA, MPTA, HKA, slope and zoom sliders all drag smoothly
- [ ] Theatre sliders drag without sticking or jumping after one step
- [ ] Switch to **LT** and check every tab — Lithuanian strings run longer and decimal commas must appear
- [ ] Cuts figure renders in both M/L and A/P, at minimum and maximum zoom
- [ ] Blade toggle changes both the drawn cut and the numbers
- [ ] Measure tab: load a radiograph, place six points, apply to plan
- [ ] Log: save a case, export the file, reload the page, import it back
- [ ] Disclaimer visible on every result view; footer credit on every tab

**Then verify the maths against three films with known PACS values.** The sign convention is the one thing the app cannot check for itself: it is internally consistent, but if the convention is mirrored every output is confidently wrong in the same direction. Ten minutes with three films settles it.

---

## What deliberately is not here

No database, no accounts, no analytics, no cookies, no third-party scripts.

Case data lives in the browser and in the JSON file the surgeon downloads. Nothing about a patient reaches any server, which is why there is no privacy policy to write, no data processing agreement to sign, and no controller obligation under GDPR. Adding a backend later is a real option, but it is a different product with real legal weight — see the notes in the project brief before going there.

---

## If something goes wrong

**Blank page.** Almost always a publish-directory error. It must be `.` with `index.html` at the repo root, not inside a folder.

**Styles or scripts blocked.** Check the browser console. If the CSP is rejecting something, you have added an external resource — find it rather than weakening the header.

**Changes not appearing.** Check the Deploys tab for a failed build, then hard-refresh. `must-revalidate` should prevent stale caching.

**Netlify asks for a build command.** It has guessed a framework. Clear the field; there is nothing to build.
