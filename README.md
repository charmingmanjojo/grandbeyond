# GRAND x BEYOND

Static site. No build step — deploy the folder as-is.

    /
    ├── index.html          main site (World Info, Login, Browser)
    ├── wrangler.jsonc      tells Cloudflare where the static files are
    ├── _headers            Cloudflare cache rules
    ├── .github/workflows/ping.yml   keeps Supabase from pausing (needs 2 secrets — see below)
    └── tavern/
        ├── tavern.html     cutscene + Hunter Registry + character sheet
        └── assets/         video, images, hunter alphabet font

## After uploading to GitHub
Add these two repository secrets (Settings → Secrets and variables → Actions)
so the keep-alive workflow can run:
- SUPABASE_URL        (Project URL from Supabase → Project Settings → API)
- SUPABASE_ANON_KEY   (anon public key from the same page)

## Connecting the site to Supabase (real accounts, real data)
Both `index.html` and `tavern/tavern.html` start in DEMO mode — the
Dev controls at the bottom of the page work, nothing touches a real
database. To switch to LIVE mode, open each file, find this block near
the top of the `<script>`, and fill in the same two values as above:

    var SUPABASE_URL = "YOUR_SUPABASE_URL";
    var SUPABASE_ANON_KEY = "YOUR_SUPABASE_ANON_KEY";

The site detects real values automatically — no other setting to flip.
Once live: the Dev buttons hide themselves, "Continue with Discord"
performs a real sign-in, a 12-digit license number is issued once per
account, the `sync-roles` Edge Function sets rank/Tavern access from
Discord roles, and the Hunter Registry + "My Entry" panel read and
write the real `characters` table. Requires the `supabase-setup.zip`
backend (schema.sql + sync-roles function) to already be set up per
SUPABASE_SETUP.md.

## Access model
- World Information + Login are public.
- The Hunter's Tavern tab only appears for accounts flagged as Hunters.
- Hunter status is granted by STAFF (Discord role sync) — not by any form on the site.
- The character sheet lives INSIDE the Tavern, because the site database is the Hunter registry.

## Secret address
The Tavern loads at `sakaba.hxa`, typed into the in-site browser.
Change `TAVERN_HOST` near the bottom of index.html to change it.

## Security note
The `is-hunter` CSS class is presentation only. Real gating must come from
Supabase Row Level Security on the character/registry tables.
