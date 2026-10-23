# VRR Departure Dashboard (prototype)

A phone-friendly installable web app for saved VRR departure boards.

## What it does
- Save multiple boards, each with a stop, daily start time, maximum results, and selected line/direction combinations.
- Search stops through the VRR EFA stop finder.
- Load departures from a selected stop and select exact line/destination combinations.
- Refresh live departures every 60 seconds while the page is open.
- Display planned and estimated times, delay, and platform when provided.
- Persist settings in local browser storage; export/import settings as JSON.
- PWA manifest and service worker for home-screen installation and offline shell caching.

## Important limitations
- This is a first-pass implementation. EFA responses vary by stop and transport operator; cancellation/disruption fields and platform values may need adjustment.
- The app requests `https://openservice.vrr.de/vrr` directly from the browser. If the API does not allow browser cross-origin (CORS) requests, stop search and live data will fail in that browser. In that case, deploy a tiny same-origin proxy/server and change `API` in `index.html` to its endpoint. Do not use an untrusted public CORS proxy for personal use.
- It refreshes only while the page is open. It does not send background notifications while closed.
- Browser local storage is local to that browser/profile. Export your settings for backup.
- The effective query time is the later of the saved time today and the current time, so old departures are not shown if you open the board after its saved time.
- Service matching currently uses line label plus destination text. EFA destination naming can vary, so verify each selected service after setup.
- A cancelled service may be omitted by the API entirely, in which case the app cannot show that departure as cancelled. Check API-provided service messages too.

## Run it
The PWA needs to be served over HTTPS (or localhost) for service-worker installation. Easiest:
1. Unzip this folder.
2. Publish the folder to a private/public static host such as GitHub Pages, Cloudflare Pages, or Netlify.
3. Open the HTTPS URL on Android Chrome.
4. Use Chrome menu -> **Add to Home screen** / **Install app**.
5. Tap **Add departure board**, search a stop, choose a result, set the time, load services, select line/direction combinations, and save.

You can test locally on a computer with `python -m http.server 8000`, then open it on the same computer at `http://localhost:8000`. For Android testing on the same Wi-Fi, use a development server and HTTPS or a secure tunnel; plain LAN HTTP may not permit PWA features.

## API references
- VRR EFA stop finder: `https://openservice.vrr.de/vrr/XML_STOPFINDER_REQUEST`
- VRR EFA departure monitor: `https://openservice.vrr.de/vrr/XML_DM_REQUEST`
- Example documentation: https://docs.openpublictransport.net/providers/vrr/

## Troubleshooting: `Failed to fetch`

This version uses `https://openservice.vrr.de/vrr`, a documented public VRR EFA base URL. If Chrome still reports `Failed to fetch`, direct browser requests may be blocked by CORS or the API may be temporarily unreachable. A static GitHub Pages site cannot proxy requests itself. The next step is to deploy a small server-side proxy (for example, a Cloudflare Worker) and change the `API` constant in `index.html` to that proxy URL. Avoid unknown public CORS proxies because they can observe your requests.
