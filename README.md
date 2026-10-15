# Water Restoration – Cloudflare Workers mirror

Static mirror of **https://water-fire-mold-restoration.pages.dev/** served from Cloudflare Workers
(static assets) at **https://water-restoration.guardianpestcontrol679.workers.dev/**.

`site/` is the unmodified build output of the main repository
(`guardianpestcontrol679-beep/water-restoration`, branch `main` → `npm run build` → `out/`).
Because it is the identical build, `sitemap.xml`, `sitemap-*.xml`, `sitemap.txt`, `robots.txt`
and every `<link rel="canonical">` point to the `pages.dev` site – exactly as on the main site.

## Deploy with Cloudflare Workers Builds (Git integration)

1. Cloudflare dashboard → **Workers & Pages → Create → Workers → Import a repository** → pick this repo.
2. **Project / Worker name:** `water-restoration` (gives the `water-restoration.<account>.workers.dev` URL).
   If a Worker with that name already exists from an earlier attempt, delete it first
   (Worker → Settings → Delete) or connect that Worker's *Settings → Build* to this repository instead.
3. **Build command:** leave empty · **Deploy command:** `npx wrangler deploy` · **Root directory:** `/`
4. Save and deploy. The first deploy uploads ~18,800 files (a few minutes); later deploys only upload changed files.

`wrangler.jsonc` already contains everything else (`assets.directory = ./site`, 404 handling,
trailing-slash handling). Do **not** change the assets directory to `.` – the repository's
`.git` folder would then be uploaded and the deploy fails with "Asset too large".

## Deploy from a terminal instead

```bash
npx wrangler login
npx wrangler deploy
```

## Updating the mirror

```bash
# in the main repository
npm run build
# then replace site/ here with the fresh out/
rm -rf ../water-restoration-worker/site && cp -r out ../water-restoration-worker/site
rm -rf ../water-restoration-worker/site/.git
cd ../water-restoration-worker && git add -A && git commit -m "Sync build" && git push
```

## Limits that matter (Workers Free plan)

| Limit | Value | This site |
|---|---|---|
| Files per deployment | 20,000 | ~18,770 |
| Max file size | 25 MiB | largest file 1.5 MB (`sitemap.txt`) |
| Static asset requests | free, unlimited | – |
