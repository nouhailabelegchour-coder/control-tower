# control-tower
# Glovo Live Ops Control Tower

Real-time store performance dashboard + Chrome Extension scraper.

## Architecture map

```
┌────────────────────────────┐      POST /api/kpi       ┌────────────────────────────┐
│ Chrome Extension           │ ───────────────────────▶ │ Next.js (App Router)       │
│ content.js scrapes:        │   KpiPayload JSON        │   /api/kpi   (ingest)      │
│  • "red X" closed markers  │ ◀───────────────────────│   /api/alerts (feed)       │
│  • opening-hour cells      │   { alerts, diagnosis } │   analyzePayload() engine  │
└────────────────────────────┘                          │   in-memory db (swap to  │
                                                         │   Redis/Postgres)          │
           ▲                                             └────────────┬───────────────┘
           │ poll 5s (GET /api/kpi)                                    │
           │                                                           ▼
┌──────────┴──────────────────────────────────────────────────────────────┐
│ Dashboard  (app/page.tsx)                                               │
│   KPI strip · LiveChart (Recharts) · AlertFeed · Diagnosis banner       │
│   LocalStorage snapshot for instant first paint                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## Getting started

```bash
npm install
npm run dev          # http://localhost:3000
```

Click **⚡ Simulate payload** to feed demo data, or load the Chrome extension:

1. `chrome://extensions` → Developer mode → Load unpacked → `./extension`
2. Visit `https://portal.glovoapp.com/dashboard` — scan runs every 60s.
3. Override the API URL in DevTools: `localStorage.setItem('ct.apiUrl', 'https://yours.vercel.app/api/kpi')`.

## Deliverables included

- `app/api/kpi/route.ts` — ingestion endpoint
- `app/api/alerts/route.ts` — alert feed endpoint
- `app/page.tsx` + `app/components/*` — dashboard UI
- `lib/anomaly.ts` — rule engine & automated diagnosis
- `extension/` — MV3 Chrome extension (content script + background worker)

Deploy to Vercel: `vercel --prod`.
