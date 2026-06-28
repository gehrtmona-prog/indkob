# Indkøb – opsætning (ca. 10 min)

Følg de tre dele i rækkefølge. Alt er gratis.

---

## Del 1 · Opret Supabase-database

1. Gå til **https://supabase.com** og log ind (gratis – brug fx Google/GitHub).
2. Klik **New project**.
   - Giv det et navn, fx `indkob`.
   - Vælg en database-adgangskode (gem den et sikkert sted – du får ikke brug for den i appen).
   - Region: vælg **Europe (Frankfurt)** eller tættest på dig.
3. Vent 1-2 min mens projektet oprettes.

## Del 2 · Opret tabellen (kør SQL)

1. I venstremenuen: klik **SQL Editor** → **New query**.
2. Indsæt hele blokken herunder og tryk **Run**:

```sql
-- Tabel til indkøbsvarer
create table if not exists public.items (
  id uuid primary key default gen_random_uuid(),
  list_id text not null,
  text text not null,
  done boolean not null default false,
  created_at timestamptz not null default now()
);

create index if not exists items_list_id_idx on public.items (list_id);

-- Realtime: send ændringer ud til alle der ser listen
alter publication supabase_realtime add table public.items;

-- Adgang: enhver med app'en kan læse/skrive (lister beskyttes af et hemmeligt list-id i linket)
alter table public.items enable row level security;

create policy "Fælles adgang via app" on public.items
  for all using (true) with check (true);
```

> **Bemærk om sikkerhed:** Listerne beskyttes ved, at deres `list_id` er et tilfældigt, umuligt-at-gætte ID (en UUID), som kun deles via invitations-linket. Det er rigeligt til en privat familie-indkøbsliste. Vil du have egentlig login senere, kan vi tilføje det.

## Del 3 · Indsæt dine nøgler i appen

1. I Supabase: klik **Project Settings** (tandhjul) → **API**.
2. Kopiér de to værdier:
   - **Project URL** (ser ud som `https://xxxx.supabase.co`)
   - **anon public** nøglen (en lang tekststreng)
3. Åbn `index.html` og find feltet `CONFIG` øverst i `<script>`. Erstat:

```js
const SUPABASE_URL = "DIN_SUPABASE_URL";
const SUPABASE_ANON_KEY = "DIN_SUPABASE_ANON_KEY";
```

med dine egne værdier. Gem filen.

---

## Del 4 · Læg appen online (så familien kan åbne den)

For at den kan blive en "rigtig app" på telefonen (ikon på hjemmeskærmen) skal den ligge på en sikker (https) adresse. Vælg én af disse gratis muligheder:

### Nemmest: Netlify Drop
1. Gå til **https://app.netlify.com/drop**
2. Træk hele mappen `indkobsapp` ind i browservinduet.
3. Du får straks et link, fx `https://glad-kat-1234.netlify.app` – det er din app.
4. (Valgfrit) Opret en gratis konto for at beholde linket permanent og kunne opdatere.

### Alternativ: Cloudflare Pages eller Vercel
Begge har "drag & drop"/Git-baseret upload og gratis https. Sig til, hvis du vil bruge en af dem, så guider jeg.

---

## Sådan inviterer du familien

1. Åbn app-linket på din telefon.
2. Tryk på **⤴ (Inviter)** øverst – det deler/kopierer linket med din listes hemmelige ID.
3. Send linket til familien (SMS, besked, hvad som helst).
4. Alle der åbner linket ser **samme liste** og opdateres i realtid. ✅

## Føj til hjemmeskærmen (så den føles som en app)

- **iPhone (Safari):** Del-knappen → "Føj til hjemmeskærm".
- **Android (Chrome):** menuen (⋮) → "Installér app" / "Føj til startskærm".

---

## Knapperne i appen

- **⤴ Inviter** – del linket til den nuværende liste.
- **＋ (øverst) Ny liste** – start en frisk, tom liste (fx til en anden lejlighed/fest). Din gamle liste forsvinder ikke; du kan vende tilbage via det gamle link.
- **Cirklen** – kryds en vare af (rykker ned).
- **×** – slet en vare.
- **Ryd afkrydsede** – fjern alt det, I har handlet.

God fornøjelse! 🛒
