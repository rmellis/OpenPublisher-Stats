<p align="center">
  <img src="https://openpublisher.app/favicon.svg" alt="OpenPublisher Logo" width="64" height="64" />
</p>

<h1 align="center">OpenPublisher Status</h1>

<p align="center">
  Live operational status and 30-day service telemetry for <a href="https://openpublisher.app">OpenPublisher</a>, hosted at <a href="https://stats.openpublisher.app">stats.openpublisher.app</a>.
</p>

<p align="center">
  <a href="https://stats.openpublisher.app">Live Status</a> &bull;
  <a href="https://openpublisher.app">Web App</a> &bull;
  <a href="https://download.openpublisher.app">Downloads</a> &bull;
  <a href="https://kb.openpublisher.app">Knowledge Base</a> &bull;
  <a href="https://github.com/rmellis/open-publisher">Main Repository</a> &bull;
  <a href="https://github.com/rmellis/open-publisher/issues">Report an Issue</a>
</p>

---

## Overview

`stats.htm` is a single, self-contained HTML dashboard with zero build steps and zero external JavaScript or CSS framework dependencies. It provides real-time operational visibility across the web application, documentation, downloads, and development infrastructure powering OpenPublisher.

Designed around OpenPublisher's dark teal (`#1e9495`) and paper white (`#f4f6f5`) visual identity, the interface reflects desktop-publishing roots with a subtle layout grid, a top millimeter-style ruler strip, dashed margin guides, and an editor-style selected text box (complete with resize handles, rotation stem, and floating style pills) framing the overall system status headline. Both the color and monochrome OpenPublisher SVG marks are embedded directly as data URIs so branding remains intact even during an upstream web hosting disruption.

## Features

- **Live Status Hero & 30-Day Health Ring:** Displays a status-reactive live indicator pill, the selected text-box headline, an animated SVG 30-day health ring showing average incident-free uptime, an online services counter (`6 / 6`), a last-checked timestamp with a `Local` / `UTC` toggle, and a 60-second auto-refresh countdown bar with a manual refresh trigger.
- **Service Breakdown (30-Day History):** Six OpenPublisher service cards with custom icons, live status pills, 30-day color-coded uptime bar strips, accurate incident-free percentages (merging overlapping incident intervals), and hover or keyboard-focus tooltips detailing any incidents for each UTC day.
- **Infrastructure Matrix Tab:** A secondary `Infrastructure` tab listing all upstream provider components with `Used by OpenPublisher` and `Not used` relevance tags, live states, and last-updated timestamps.
- **Active Notices & Filtered Incident Log:** Automatically reveals an `Active Notices` panel when ongoing incidents or scheduled maintenance affect OpenPublisher services. Below the service grid, the `Incident log` displays recent relevant incidents (3 by default with a `Show more` / `Show fewer` toggle up to 10), formats HTML breaks into clean paragraphs, provides expandable update timelines, and filters out unrelated upstream tools.
- **Offline & Brand Resilience:** Embedded SVG data URIs prevent missing-logo states if `openpublisher.app` is unreachable, and the transparency cards remind users that OpenPublisher desktop editions (Windows, macOS, Linux, and FreeBSD) continue editing and exporting `.pub` and PDF layouts offline during web outages.
- **Responsive & Accessible:** Adapted for desktop, mid-width, and mobile viewports (compact single-row mobile header, horizontally scrollable infrastructure table, responsive health ring placement), using `aria-live` updates, semantic `tablist`/`tabpanel` roles, keyboard-focusable uptime bars, and `prefers-reduced-motion` support.

## Monitored Services

Each OpenPublisher service is mapped to its corresponding upstream infrastructure component in `stats.htm`:

| Service | Scope | Upstream Component |
| --- | --- | --- |
| **Web App & Template Library** | Browser-based desktop publishing editor, assets, and public templates at `openpublisher.app` | `Pages` |
| **Knowledge Base** | Online manuals, migration guides, and help documentation at `kb.openpublisher.app` | `Pages` |
| **Desktop Downloads** | Installers and builds for Windows, macOS, Linux, and FreeBSD (`download.openpublisher.app`) | `Packages` |
| **Source Code Access** | Git clone, pull, and browsing for `rmellis/open-publisher` | `Git Operations` |
| **Builds & Site Deployments** | Automated CI workflows compiling releases and publishing site updates | `Actions` |
| **Community & Bug Reports** | Public bug tracking, feature requests, and version check metadata | `Issues` |

`API Requests` is also tracked in `extraRelevantComponents` so API-related events appear in the `Infrastructure` matrix and `Incident log`. Unrelated upstream components are excluded from the overall status calculation and filtered incident history.

## How It Works

1. Every 60 seconds (or on manual refresh), the page fetches live telemetry directly from the public GitHub Status API v2 endpoints with `cache: "no-store"`:
   - `https://www.githubstatus.com/api/v2/summary.json`
   - `https://www.githubstatus.com/api/v2/incidents.json`
2. The overall hero status (`operational`, `under_maintenance`, `degraded_performance`, `partial_outage`, or `major_outage`) is computed strictly from the mapped OpenPublisher components.
3. For each service, `stats.htm` constructs 30 UTC daily buckets, determines the worst impact per day, and merges overlapping incident time windows to compute the 30-day incident-free percentage.
4. If the status feed cannot be reached, an error notice is shown with a direct link to `https://www.githubstatus.com/` while the auto-refresh timer continues retrying.

## Local Preview

Because `stats.htm` is completely static, you can preview it directly in any modern browser or serve it with a local HTTP server:

```bash
python -m http.server 8000
```

Then visit `http://localhost:8000/stats.htm`.

## Configuration & Customization

All service mappings and telemetry behavior live in the `CONFIG` object and adjacent constants inside `stats.htm`:

- `CONFIG.refreshIntervalSeconds` (`60`): Auto-refresh interval in seconds.
- `CONFIG.historyDays` (`30`): Number of UTC days displayed in each service uptime strip.
- `CONFIG.services`: Array of OpenPublisher services (`id`, `name`, `description`, `githubComponent`).
- `CONFIG.extraRelevantComponents` (`["API Requests"]`): Additional upstream components included in incident filtering and relevance tagging.
- `INITIAL_HISTORY` (`3`) & `MAX_HISTORY` (`10`): Default and expanded incident counts in the `Incident log`.

Visual tokens (such as `--brand: #1e9495`, status colors, and Google Fonts for `Bricolage Grotesque`, `Inter`, and `JetBrains Mono`) are defined at the top of `<style>` under `:root`.

## Deploying to GitHub Pages (`stats.openpublisher.app`)

1. **Set the entry file:** Rename `stats.htm` to `index.html` so GitHub Pages serves the status page automatically at `/`.
2. **Enable GitHub Pages:** In the repository's **Settings -> Pages**, choose **Deploy from a branch** and select your main branch root (`/`).
3. **Attach the custom domain:** Set the custom domain to `stats.openpublisher.app` (which creates/uses a `CNAME` file), enable **Enforce HTTPS**, and point your DNS `CNAME` record for `stats` to `<username-or-org>.github.io`.

## License

Free and open source software (**GPLv2**). See the main [OpenPublisher repository](https://github.com/rmellis/open-publisher) for full project details.
