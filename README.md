# GAMEVERSE PH — Firebase security setup

## Current status
- Firebase Authentication is used by the static game lobby.
- The lobby's practice points and activity charts are browser-local demo data.
- Firestore wallet, points ledger, mission claims, and payout methods are **not live**.
- Cash-in, cash-out, ad rewards, and real-money balances must remain disabled.

## Firestore rules
The included `firestore.rules` intentionally denies all client-side writes to reward and wallet records. A signed-in player can read only documents beneath their own `users/{uid}` path. This is a safety baseline, not a complete rewards backend.

Deploy these rules from the repository root using the Firebase CLI after selecting the correct Firebase project:

```bash
firebase login
firebase use gameverse-102213
firebase deploy --only firestore:rules
```

Before deployment, confirm in the Firebase console that the active project is `gameverse-102213`. Never paste service-account keys, private keys, payment secrets, OTPs, or wallet recovery phrases into this repository or a chat.

## Required before real rewards
1. Build a trusted server backend (for example, Firebase Cloud Functions or Cloud Run) using Firebase Admin SDK.
2. Verify Firebase ID tokens on every protected request.
3. Keep all balance changes server-side. Use an append-only ledger with unique transaction IDs and idempotency checks.
4. Verify ad network reward callbacks server-to-server before crediting a mission.
5. Add rate limits, duplicate-claim prevention, audit logs, and abuse monitoring.
6. Complete provider onboarding and integrate the approved payment API using server-side secrets.
7. Test Firestore rules and backend authorization before enabling any reward or payout button.

**Important:** GitHub Pages is static hosting. It cannot securely hold payment credentials or perform trusted reward verification by itself. Do not enable cash-out based only on browser JavaScript or localStorage.
