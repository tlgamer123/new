# 🪐 Planet Smasher — Vercel Deploy Package

This folder is ready to deploy to Vercel **as-is**. The game works 100% on
Vercel immediately. The Hall of Destruction leaderboard works too, once you
connect a free database (2-minute optional step below).

What's already configured for you:

- `package.json` → `postinstall` runs `prisma generate` (Vercel needs this)
- `src/lib/db.ts` → starts cleanly even with **no database configured**
- `/api/destructions` → returns an empty leaderboard instead of crashing
  when storage isn't connected

---

## ⚠️ Read this first: how Vercel accepts deployments

Vercel does **not** have an "upload folder" button for Next.js apps (it only
accepts ZIP drag-and-drop for static sites). Your app needs to be *built*,
so Vercel offers exactly two supported ways to get this folder in:

1. **GitHub upload (recommended — zero command line)** → Method A
2. **Vercel CLI (one command)** → Method B

Pick either one. Method A is easiest if you've never used a terminal.

---

## ✅ Method A — GitHub drag & drop (no commands)

### Step 1 — Unzip this package
Extract the ZIP. You get a folder named `planet-smasher`.

### Step 2 — Create an empty GitHub repo
1. Go to **https://github.com/new**
2. Repository name: `planet-smasher` (anything works)
3. Choose **Private** (recommended)
4. Click **Create repository**

### Step 3 — Upload the folder (drag & drop)
1. On the new repo page, click the link
   **"uploading an existing file"**
2. Open the extracted `planet-smasher` folder, press **Ctrl/Cmd + A** to
   select everything *inside* it (package.json, src, prisma, public, …),
   and **drag all of it** onto the GitHub upload page
3. Wait for the upload to finish (it's small — under 1 MB)
4. Click **Commit changes**

> 💡 Do NOT upload `node_modules` (it's not in the ZIP anyway) and never
> upload your `.env` file — it's not in the ZIP either.

### Step 4 — Import into Vercel
1. Go to **https://vercel.com/new**
2. If asked, sign up / log in with your **GitHub account**
3. Find `planet-smasher` in the "Import Git Repository" list → click **Import**
4. Leave everything at defaults — no environment variables required
5. Click **Deploy** and wait ~1–2 minutes

🎉 Done. Vercel gives you a URL like
`https://planet-smasher-yourname.vercel.app` — open it and smash planets.

---

## ✅ Method B — Vercel CLI (one command)

```bash
# 1. install the CLI (needs Node.js 18+ on your machine)
npm i -g vercel

# 2. inside this folder — it walks you through login + deploy
cd planet-smasher
vercel

# 3. ship it to production
vercel --prod
```

First run asks: "Set up and deploy?" → **Y**, "Which scope?" → your account,
"Link to existing project?" → **N**, then just press Enter to accept the
detected settings. It deploys and prints your live URL.

---

## 🏆 Optional (2 min): make the leaderboard persist on Vercel

Vercel's servers are read-only, so the default SQLite file can't save scores
there. The game itself is unaffected — but if you want the Hall of
Destruction to store records, use a free **Neon** Postgres database:

### Step 1 — Create a free database
1. Go to **https://neon.tech** → sign up (free, no card)
2. Create a project → copy the **connection string**. It looks like:
   ```
   postgresql://user:password@ep-xxx-pooler.region.aws.neon.tech/neondb?sslmode=require
   ```

### Step 2 — Switch the schema to Postgres
Open `prisma/schema.prisma` and change **one word**:

```prisma
datasource db {
  provider = "postgresql"   // was: "sqlite"
  url      = env("DATABASE_URL")
}
```

### Step 3 — Create the table (run once, from this folder)
```bash
npm install                     # or: bun install
set DATABASE_URL=<paste your Neon string>        # Windows (cmd)
# export DATABASE_URL=<paste your Neon string>   # macOS / Linux
npx prisma db push              # creates the Destruction table on Neon
```

### Step 4 — Add the env var on Vercel
1. Open your project on **vercel.com** → **Settings → Environment Variables**
2. Add:
   - Key: `DATABASE_URL`
   - Value: your Neon connection string
3. **Deployments → Redeploy** (pick the latest → Redeploy)

That's it — every destroyed planet is now saved permanently and shows up in
the Hall of Destruction on any device.

> Running locally? Just keep `provider = "sqlite"` and a `DATABASE_URL="file:../db/custom.db"`
> in a local `.env` file, then `npx prisma db push`.

---

## 📝 Notes

- **Ads (A-ADS)**: the ad unit uses a protocol-relative URL, so it works
  automatically on Vercel's HTTPS domains. Zone ID 2460685 is already set.
- **Node version**: Vercel auto-uses Node 20+ for Next.js 16. Nothing to set.
- **bun.lock**: included for reproducible installs. Vercel detects it and
  installs with Bun. If you prefer npm, delete `bun.lock` before uploading.
- **Re-deploying changes**: after editing code on GitHub (web editor or push),
  Vercel re-deploys automatically. No manual steps.
