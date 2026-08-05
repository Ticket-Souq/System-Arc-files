# TicketSouq LikeC4 Workspace

Interactive, auto-layout rendering of the TicketSouq **C4 architecture model** using [LikeC4](https://likec4.dev/).

The 14 diagrams in [`docs/c4/`](../docs/c4/) (Mermaid, rendered natively on GitHub) are transcribed
into LikeC4 DSL here: the same model, but with automatic layout and click-through navigation
(System Context → Containers → Component views).

`docs/c4/*.md` remains the **single source of truth**; this workspace is a derived, interactive view.

## Prerequisites

- Node.js 20+ (developed against v24) and npm.

## Commands

| Command | Description |
|---------|-------------|
| `npm install` | Install the LikeC4 CLI |
| `npm run dev` | Start the local dev server (`likec4 serve`) with live reload |
| `npm run build` | Build a static site to `dist/` |
| `npm run preview` | Preview the production build locally |
| `npm run validate` | Validate syntax, semantics and layout drifts |
| `npm run format` | Reformat the `.c4` sources |
| `npm run export` | Export all views to PNG under `assets/` |

All commands run from this directory.

## File Layout

| File | Contents |
|------|----------|
| `src/spec.c4` | Custom element kinds and shapes (person / storage / queue) |
| `src/model.c4` | Full model: actors, TicketSouq system with all components, data stores, external systems, and all relationships |
| `src/views.c4` | The 14 views (system context, containers, 12 component diagrams) |

## Views vs. Source Docs

| LikeC4 view | Source document (`docs/c4/`) |
|-------------|------------------------------|
| `index` (System Context) | `system-context.md` |
| `containers` | `containers.md` |
| `apiGateway` | `components/api-gateway.md` |
| `configServer` | `components/config-server.md` |
| `discoveryServer` | `components/discovery-server.md` |
| `userService` | `components/user-service.md` |
| `eventService` | `components/event-service.md` |
| `venueService` | `components/venue-service.md` |
| `ticketService` | `components/ticket-service.md` |
| `reservationService` | `components/reservation-service.md` |
| `paymentService` | `components/payment-service.md` |
| `notificationService` | `components/notification-service.md` |
| `auditService` | `components/audit-service.md` |
| `analyticsService` | `components/analytics-service.md` |

## Fidelity Notes

- Component views reproduce the edges of the corresponding `C4Component` diagram; container-level
  routes / Feign / platform / data-store edges (shown only in `containers.md`) are excluded from the
  component views, and component-level edges are re-included explicitly where an `exclude` would
  otherwise suppress them.
- The "All Backend Services" box exists only in the config-server and discovery-server views and is
  excluded from the containers view.
- `docs/c4/*.md` remains authoritative — if a diagram changes, update the Mermaid doc first, then
  mirror the change here.
