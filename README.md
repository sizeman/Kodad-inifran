# Kodad Inifrån — Driftsättningsguide

## Översikt
En progressiv webb-app (PWA) byggd med React + Vite, Supabase (databas + auth) och Claude API (AI-reflektion). Driftsätts gratis på Vercel.

---

## Steg 1 — Skapa tabeller i Supabase

Gå till **supabase.com** → ditt projekt → **Table Editor** → **New table**

### Tabell 1: `tkhr_logs`
- Name: `tkhr_logs`
- Enable Row Level Security (RLS): **PÅ**
- Kolumner att lägga till (utöver id och created_at som skapas automatiskt):
  - `user_id` — typ: `text`
  - `tanke` — typ: `text`
  - `kansla` — typ: `text`
  - `handling` — typ: `text`
  - `resultat` — typ: `text`
  - `ny_tanke` — typ: `text`

### Tabell 2: `reflections`
- Name: `reflections`
- Enable Row Level Security (RLS): **PÅ**
- Kolumner:
  - `user_id` — typ: `text`
  - `prompt` — typ: `text`
  - `user_message` — typ: `text`
  - `ai_response` — typ: `text`

### RLS-policyer (viktigt!)
För varje tabell, gå till **Authentication → Policies → New Policy → Get started quickly → Enable read access for authenticated users**

Skapa sedan två policyer per tabell:
1. **SELECT** — `auth.uid()::text = user_id`
2. **INSERT** — `auth.uid()::text = user_id`

---

## Steg 2 — Skaffa Claude API-nyckel

Gå till **console.anthropic.com** → **API Keys** → **Create Key**

Kopiera nyckeln — den börjar med `sk-ant-`

---

## Steg 3 — Ladda upp till GitHub

1. Skapa ett nytt repository på **github.com** (döp det till `kodad-inifran`)
2. Ladda upp alla filer från den här mappen till repositoryt
3. Se till att `.env.local` **inte** laddas upp (den innehåller hemliga nycklar)

---

## Steg 4 — Driftsätt på Vercel

1. Gå till **vercel.com** → **New Project**
2. Importera ditt GitHub-repository
3. Klicka på **Environment Variables** och lägg till:

| Namn | Värde |
|------|-------|
| `VITE_SUPABASE_URL` | `https://lksfwbrktbybtokltmijg.supabase.co` |
| `VITE_SUPABASE_ANON_KEY` | `eyJhbGci...` (din anon key) |
| `ANTHROPIC_API_KEY` | `sk-ant-...` (din Claude-nyckel) |

4. Klicka **Deploy**

Vercel bygger appen automatiskt. Om några minuter är den live på en URL som liknar `kodad-inifran.vercel.app`

---

## Steg 5 — Koppla din domän (valfritt)

I Vercel → ditt projekt → **Settings → Domains** → lägg till din domän.

Hos din domänleverantör pekar du domänen mot Vercel enligt deras instruktioner.

---

## Steg 6 — Konfigurera Supabase Auth

I Supabase → **Authentication → URL Configuration**:
- Site URL: `https://din-domän.se`
- Redirect URLs: `https://din-domän.se`

---

## Projektstruktur

```
kodad-inifran/
├── api/
│   └── chat.js          ← Serverless funktion (Claude API)
├── src/
│   ├── App.jsx          ← Hela appen
│   └── main.jsx         ← Entry point
├── index.html
├── vite.config.js
├── package.json
└── .env.local           ← Lägg INTE upp på GitHub
```

---

## Felsökning

**Appen visas inte:** Kontrollera att build-kommandot är `vite build` i Vercel

**AI svarar inte:** Kontrollera att `ANTHROPIC_API_KEY` är inlagd i Vercel environment variables

**Inloggning fungerar inte:** Kontrollera att Site URL är inlagd i Supabase Authentication

**Databasen sparar inte:** Kontrollera att RLS-policyerna är skapade korrekt

---

Frågor? Kontakta stefan@isenberg.se
