# Bukti — affiliate workspace

Bukti is a development application for one affiliate-business owner. The name means “evidence” in Indonesian. It does not sell products, accept payments, buy inventory or fulfil orders.

## Open the development version

The local preview runs at **http://localhost:8787** while its server is running. Open `data/owner-login.txt` for your randomly generated private owner login. Do not share or commit that file. The live database starts empty; test records live in separate test databases.

From PowerShell in `C:\Users\michelle_login\bukti`:

```powershell
npm ci
npm run setup
npm run build
npm run server
```

Open a second terminal to run scheduled work locally:

```powershell
npm run worker
```

In Automation controls, select **Set up job records**. Enable the jobs you want. The report job deliberately reports a setup failure until a provider adapter is implemented. A local worker continues without an open browser, but stops if the computer or process stops. This is not verified hosted continuous operation.

## Your first real recommendation

1. Set your intended shoppers' country, content language and currency in Connections & settings. The starting US/English/USD values are editable defaults, not an assumption about your audience.
2. Add a full Amazon, eBay or Etsy product link. Write original, supported notes about who it suits, benefits and limitations.
3. Add source URLs, dates, periods, countries, observations and limitations. Approval needs evidence from at least two different publishers, at least one independently checked source, and observations within 90 days. This is an evidence-completeness rule, not a demand or sales score.
4. Optionally enter an exact English Wikipedia article title and run **Refresh topic interest**. Wikimedia supplies topic page views; those are attention only. The observation explicitly states that it is not product demand or sales evidence.
5. Approve the product. Generate ten templates in Creative studio, edit them, approve the copy, and download individual SVG artwork or the ZIP containing all artwork, editable JSON and video scripts.
6. After a marketplace approves your actual website, save that website URL and approval reference under its connection. Paste the marketplace-issued full affiliate link into the product. The app preserves tracking parameters; it does not generate or verify affiliate account IDs.
7. Save edits, reapprove the product and publish it, or place it in the approval queue. Editing a product returns it to draft and removes its public page until you approve it again.
8. Visitors browse `/shop` and `/p/product-slug`. They buy directly from the named marketplace. Counted clicks do not create commission or sale records.
9. Import commission reports through the normalized CSV format described in `docs/IMPORTS.md`. Record costs and review reporting completeness.

## Android and iOS

Both use the same Expo Router screens and backend API. Mobile is a management application; there is no shopper navigation button in the native app.

```powershell
# Set this to the same deployed backend used by web, never a secret:
$env:EXPO_PUBLIC_API_URL="https://YOUR-BACKEND"
npm start
```

Use an Expo Go version compatible with SDK 57 or create a development build. The EAS `preview` profile creates an Android APK without requiring expo-dev-client. The `development` profile needs `npx expo install expo-dev-client` before use.

```powershell
npx eas-cli login
npx eas-cli build:configure
npx eas-cli build --platform android --profile preview
npx eas-cli build --platform ios --profile preview
```

These commands are instructions only; no build service, billing or app-store submission was activated. EAS needs your account and project ID. iOS device distribution needs Apple signing credentials and registered devices. Windows cannot run the iOS simulator. Local Android builds need Java and Android SDK tools, which were not available in this environment. Native JavaScript/Hermes bundles compiled successfully; no APK or IPA was produced or device-tested.

## Architecture

- `app/`, `src/`: React Native, Expo Router and TypeScript management screens for web, Android and iOS.
- `server/api.ts`: one authenticated backend with server-enforced owner sessions.
- `server/local.ts`, `server/local-db.ts`: local HTTP server and persistent SQLite using Node's SQLite driver. Node 24.12+ or 26 is recommended.
- `server/worker.ts`: the same application handler for Cloudflare Workers, with D1 persistence and a scheduled handler.
- `db/schema.ts`, `drizzle/`: versioned database schema and migration. Never edit an already deployed migration.
- `server/jobs.ts`: persistent job definitions, run records, locking, capped retries, pauses and approval checks.
- `server/public.ts`: server-rendered public HTML, metadata, searchable collections, marketplace filters and sitemap. New products appear without rebuilding the Expo bundle.
- `server/ai.ts`: optional server-only OpenAI Responses adapter with structured drafts and an atomic monthly spending reservation.
- `tests/`: isolated workflow and security tests; synthetic fixtures are labelled and never seeded into live data.

See `docs/DEPLOYMENT.md`, `docs/ACCOUNTS-AND-COSTS.md`, and `docs/STATUS.md` for setup, verified sources, limitations and test evidence.

## Tests

```powershell
npm run typecheck
npm test
npm run build
npm run build:native
node scripts/verify-bundles.mjs
```

Do not treat successful bundles as device tests, mocked AI tests as live API access, or a saved scheduler configuration as verified continuous operation.
