# FIFA World Cup 2026 — Stadium Intelligence Command Center (Prototype)

An interactive prototype of the **Organizer AI Operations Copilot** from the full
"FIFA World Cup 2026 AI Stadium Intelligence Platform" concept. It demonstrates the
core "wow factor" flow: an agent swarm reasoning through a live incident in real time,
with explainable output (`Prediction → Confidence → Reasoning → Action → Impact`).

**[Live status: single-file prototype, not the full production app — see Scope below]**

## What this is

A single self-contained `index.html` — no build step, no server, no dependencies to
install. It renders a glassmorphism command-center UI with:

- A live zone-density heatmap (8 stadium zones) with ambient simulated drift
- A "Simulate Transport Delay" trigger that flags a zone and fires three AI agents
  (Transport, Crowd, Volunteer) in sequence
- Explainable-AI cards for each agent's response, following the
  `Prediction → Confidence % → Reasoning → Action → Estimated Impact` format
- A match-readiness score and risk-level indicator that react to the incident
- Dark/light theme toggle, operations log, role switcher (Organizer / Fan / Volunteer / Staff)

## Scope — what's real vs. mocked

| Area | Status |
|---|---|
| UI, layout, theming, animations | Fully built |
| Zone heatmap | Simulated client-side (no Mapbox) |
| Agent reasoning (Transport/Crowd/Volunteer) | **Live AI call** when run inside a Claude.ai artifact sandbox; falls back to static mock JSON otherwise (see below) |
| Organizer dashboard | Fully built |
| Fan / Volunteer / Staff dashboards | Placeholder panels only |
| Auth, database (Supabase) | Not implemented |
| Real map (Mapbox) | Not implemented |
| Backend / API routes | Not implemented |

This was built as a fast prototype to validate the UX and the "explainable agent swarm"
concept before investing in the full Next.js + Supabase + Mapbox + Gemini stack described
in the original plan.

### About the AI calls

The `callAgent()` function in `index.html` calls `https://api.anthropic.com/v1/messages`
directly from the browser. **This endpoint is only reachable from inside a Claude.ai
artifact sandbox**, which proxies and authenticates the request. Opened as a plain file
or hosted elsewhere (GitHub Pages, Vercel, etc.), that call will fail and the app
gracefully falls back to the static responses in `fallbackData()` — the UI and animations
still work, just without live model output.

To get live AI reasoning outside of Claude.ai, the fetch needs to go through your own
backend route holding a real Anthropic API key (never call the API with a key from
client-side JS in production) — see Roadmap below.

## Running it

No install needed:

```bash
# just open it
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
```

Or in VS Code, install the **Live Server** extension and right-click
`index.html` → "Open with Live Server" for auto-reload while editing.

## Project structure

```
.
├── index.html    # entire app: markup, styles, and logic in one file
├── README.md
└── LICENSE
```

## Roadmap toward the full spec

The original plan (Next.js + Supabase + Mapbox + Gemini, multi-role dashboards,
full agent swarm with 6 agents, RBAC auth, real-time websockets) is a multi-week
build requiring provisioned services and API keys:

1. Scaffold a real Next.js (App Router) project with the feature-based architecture
2. Move `callAgent()` behind a Next.js Server Action / Route Handler with a
   server-held Gemini (or Claude) API key
3. Wire up Supabase for auth (RBAC: Fan/Volunteer/Staff/Organizer) and persistence
   (incidents, sensors, AI logs)
4. Swap the CSS-grid heatmap for Mapbox GL JS with real heatmap layers
5. Build out the Fan, Volunteer, and Staff dashboards
6. Add the remaining agents (Emergency, Sustainability, Accessibility)
7. Testing (Jest, Playwright, axe-core, Lighthouse) per the original verification plan

## License

MIT — see [LICENSE](./LICENSE).
