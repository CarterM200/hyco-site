# HyCo — landing site + waitlist

A self-contained static site: `index.html` (landing + waitlist) and three legal pages
(`privacy.html`, `terms.html`, `disclaimer.html`). No build step, no framework.

---

## 1. Make the waitlist actually capture emails

The form is pre-wired to your existing **Supabase** project (free). Two steps:

### a) Create the table (one time)
In Supabase → **SQL Editor** → paste and run:

```sql
create table if not exists public.waitlist (
  id uuid primary key default gen_random_uuid(),
  email text not null unique,
  source text,
  created_at timestamptz default now()
);

alter table public.waitlist enable row level security;

-- allow anonymous visitors to INSERT (join the list), but not read
create policy "anon can join waitlist"
  on public.waitlist for insert
  to anon
  with check (true);
```

### b) Add your keys to `index.html`
Near the bottom of `index.html`, find the `WAITLIST` config block and fill in:

```js
const WAITLIST = {
  mode: "supabase",
  supabaseUrl:  "https://YOUR-PROJECT.supabase.co",   // Supabase → Project Settings → Data API → URL
  supabaseAnonKey: "YOUR-PUBLISHABLE-ANON-KEY",        // the public/anon "publishable" key (safe in a browser)
  formspreeId:  "YOUR-FORM-ID"
};
```

That's it — submissions appear in the `waitlist` table. View them in Supabase → Table Editor,
or export to CSV any time.

**Prefer no database?** Set `mode: "formspree"`, create a free form at formspree.io, and paste its
form ID into `formspreeId`. Done.

---

## 2. Fill in the legal placeholders

Open each of `privacy.html`, `terms.html`, `disclaimer.html` and replace every highlighted
`[placeholder]` (entity name, address, contact email, effective date, etc.).
Then **delete the orange "Draft template" banner** at the top of each page.

> These are solid starting templates, **not** legal advice. Because HyCo handles health data and
> prescribes training intensity, have a qualified UK solicitor review all three before you publish.

---

## 3. Deploy (Vercel — same as the app)

Easiest path on iPad:
1. Put this `hyco-site` folder in a GitHub repo (or its own folder in your existing one).
2. In Vercel → **Add New → Project** → import the repo.
3. Framework preset: **Other** · Build command: *(leave blank)* · Output directory: `.` (root).
4. Deploy. Add your custom domain (e.g. `hyco.app`) in Vercel → Project → Domains.

Or drag the folder onto **netlify.com/drop** for an instant URL.

---

## Files
- `index.html` — landing page + waitlist (edit the WAITLIST config)
- `privacy.html` — Privacy Policy (UK GDPR)
- `terms.html` — Terms of Service
- `disclaimer.html` — Health & Fitness Disclaimer
- update the contact email (`hello@hyco.app`) across all pages to your real address
