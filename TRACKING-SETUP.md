# LUNO shipment tracking

The customer page is native Hebrew/RTL React UI. It has no external tracking link,
provider script, iframe or provider logo. No sample shipment is presented as real.
Without credentials it explicitly says tracking is being set up and offers LUNO support.

## Activate live data

1. Open an API account with 17TRACK (https://api.17track.net/en/doc). Current docs,
   checked 2026-09-13, say new accounts receive 200 one-time free tracking quotas;
   monthly free allocation ended January 7, 2026. Check current account terms first.
2. Set `TRACKING_API_KEY` as a **server secret** in Sites runtime environment settings
   and deploy the saved site version. Never put the token in GitHub or a VITE variable.
3. Register each actual AliExpress shipment in the API dashboard and set its tag to
   exactly `LUNO`. Select the correct carrier if detection is ambiguous. Complete any
   carrier-required shipment details in that dashboard. Customer searches do not register
   shipments or spend registration quota. The endpoint only reads previously registered,
   LUNO-tagged shipments. This currently requires merchant entry after shipment.
4. Verify a real LUNO shipment after setup. Live carrier data has NOT been verified yet:
   no API key or real registered shipment was available during implementation.

## GitHub Pages

`tracking.html` uses the same LUNO UI and calls the existing hosted LUNO server over
HTTPS. GitHub Pages alone cannot keep an API secret or run the tracking backend.
The hosted server must remain running. It currently allows the user's known origin
`https://airsoftil.github.io`; for another custom domain set the comma-separated server
variable `TRACKING_ALLOWED_ORIGINS` with the exact origins. No wildcard CORS or credentials.

## Data and limits

Only status and shipment events (description, time, location) leave the server; account
tags and structured recipient names, addresses and phone fields are omitted. Tracking
numbers act as lookup keys. Event descriptions remain carrier-supplied text, rendered
as plain text, and may contain carrier-provided details. No order/payment lookup exists.

The server uses `/track/v2.4/gettrackinfo` with a 10-second timeout, short bounded memory
cache and per-worker request pacing. These are not global limits across instances;
provider throttling returns a retryable customer message. Add account-wide rate limiting
if traffic grows. No automatic registration, subscription, payment or webhook was created.

The provider adapter and failure paths are tested with fixtures. A later AliExpress/GROW
automation can register shipment numbers after verified ordering without changing the UI.
