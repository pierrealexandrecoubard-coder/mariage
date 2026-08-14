# Handoff: Site de Mariage — Léa & Pierre-Alexandre

## Overview
Site privé de mariage (invités connectés par code d'invitation individuel) pour Léa & Pierre-Alexandre, mariage le 14 août 2027 au Domaine de Meaucé (Le Perche). Une seule page longue défilante, multilingue (FR / EN / SK), avec RSVP complet, covoiturage entre invités, mini-jeu, et un back-office admin.

## About the Design Files
The bundled file is a **design reference built as an HTML prototype** (inline-styled, single-file "Design Component" runtime specific to our design tool — NOT a framework to copy verbatim). Treat it as the visual/interaction/content spec. Recreate the design in whatever stack the target project uses. If no project exists yet, this is a small marketing/RSVP site — plain HTML/CSS/JS, or a lightweight framework (Astro/Next), is a natural fit, but the RSVP/covoiturage data really should move to a real backend/datastore (see State Management below) rather than staying client-side.

## Fidelity
**High-fidelity.** Colors, type, spacing, copy (French primary, with English/Slovak translations) and behavior described below should be recreated as closely as possible.

## Assets — important
All photos referenced by the design are real, final images, bundled in `source/uploads/`. The HTML references them as relative paths like `uploads/Maximilien-head-crop.jpg`. **Keep the `uploads/` folder name and structure when you copy the design into a new project**, or update every `src`/`image-slot` reference if you rename/move it — this was the cause of broken images in a previous export. A few placeholders (`<image-slot>` with no `src`) are intentionally still empty (e.g. team members with no photo yet, some accommodation photos) — the couple will supply these later.

## Global Design System

### Colors
- Ink (primary text/dark bg): `#20261f`
- Cream (page background): `#fffdf8`
- Warm sand (alt section bg): `#efe4d0` / `#faf5ec`
- Sage green (accent, CTAs): `#a9c2b3`
- Deep sage (links, italics, eyebrows): `#4f6f63`
- Body text: `#4a443a`
- Error/soft red: `#e29a94`

Section backgrounds rotate between cream, sand and ink — max a few backgrounds, kept consistent per section type.

### Typography
- Display/headers: **Fraunces** (variable, opsz 9–144, weight 300–700, italic supported). Headlines use italic for accent words (e.g. "l'autel", "&").
- Body/UI: **Inter** (weight 200–600).
- Google Fonts import: `Fraunces:opsz,wght,ital@9..144,300..700,0..1` + `Inter:wght@200..600`.
- Uppercase eyebrow labels: `letter-spacing:.1em`, italic, sage color.

### Motion
- Scroll-linked reveal on every section (opacity/translateY, IntersectionObserver-driven via `reveal.<section>` / `regReveal.<section>` state).
- Hero photo crossfade + parallax on mouse move.
- Bento-grid tilt-on-hover for story photos.
- Toast notifications for saved actions (covoiturage, hébergement choice, etc).

## Screens / Sections (in page order)
1. **Login gate** — full-screen photo background, invitation-code input (`GUESTS` lookup by code), error state, admin access also reachable from here.
2. **Nav** — sticky, links to Histoire / Programme / Infos / Jeu / FAQ, language switcher (FR/EN/SK), "Confirmer ma présence" CTA → `#rsvp`, logout button.
3. **Hero** — full-bleed photo crossfade, couple names, date/venue, live countdown to 14/08/2027.
4. **Notre histoire** — bento-grid of real couple photos with hover tilt + narrative text blocks.
5. **Équipe du jour** — témoins, demoiselles/garçons d'honneur, coordinatrice (Tiphany), each with photo + role, sourced from `TEMOINS_LIST` / `DEMOISELLES_LIST` / `GARCONS_LIST` / `COORDINATION_LIST`.
6. **Programme** — ceremony/reception events by day (vendredi/samedi/dimanche), each with calendar-add (Google/ICS) and GPS buttons (Google Maps / Apple Maps / Waze). Shows a personal note if the logged-in guest is a témoin/honneur.
7. **Dress code** — palette + do's/don'ts, tailored by role (demoiselle/garçon d'honneur vs. regular guest), with an FAQ link.
8. **Infos pratiques** — getting there (car/train), train pickup coordination.
9. **Domaine** — venue hero photo + gallery + link to the venue's own site.
10. **Hébergement sur place** — on-site lodging options/prices, VIP guests see "offert" messaging; Friday-night option only shown to guests invited Friday.
11. **Gîtes à proximité** — nearby off-site lodging, linked list.
12. **RSVP** (`#rsvp`) — presence yes/no, per-event-step selection (nothing pre-checked — guest must actively select), transport, on-site lodging, Friday dinner (conditional on Friday presence), allergies, dietary notes, free-text message. Submission stored per-code.
13. **Covoiturage** (conditional section, only for guests who picked "je cherche" / "je propose") — list of ride offers/requests from all guests, contact reveal gated by consent, add/edit/remove own entry.
14. **Jeu** — small canvas mini-game ("Léa & PA en route vers l'autel"): jump to avoid hay bales, collect hearts, 3 lives, saves score to a local leaderboard.
15. **FAQ** — accordion, several answers are dynamic based on the guest's actual RSVP selections (e.g. breakfast/meals included, dress code, lodging, carpool).
16. **Galerie** — photo grid.
17. **Contact** — "Une question ?" with Léa & PA's emails.
18. **Footer** — credits + link to the admin overlay.
19. **Admin** — password-gated overlay: guest list, RSVP stats, leaderboard, reset-all-responses action.

## Interactions & Behavior
- **Auth**: `codeInput` → `onLoginSubmit` checks `GUESTS[code]`; persists to `localStorage` (`wedding_logged_code`) so returning on the same browser skips login.
- **Per-guest RSVP persistence**: `localStorage.setItem("rsvp_v8_" + code, ...)`; `loadRsvp(code)` always resets to a blank state object before applying saved data, so switching guest codes on the same device never leaks a previous guest's answers.
- **FAQ personalization**: several FAQ answers are computed live from the guest's actual checked RSVP steps (not just which steps they were invited to), e.g. "did you say yes to Friday night + brunch" drives the breakfast-inclusion answer.
- **Covoiturage**: entries are read fresh from `localStorage` on every render (loop over all `GUESTS` codes), so a new offer appears immediately after saving, no reload needed. Only the entry's own author sees/can use the delete button.
- **Language**: `TRANSLATIONS` object keyed `fr`/`en`/`sk`, switched via nav pills, persisted to `localStorage`.
- **External links** (maps, venue site, lodging, train station, Revolut) open in a new tab (`target="_blank"`); in-page nav anchors do not.

## State Management
Representative state keys: `loggedIn`, `code`, `lang`, `presence`, `etapesSel` (per-step checkboxes), `transport`, `hebergement`, `dinerVendredi`, `allergiesSel`, `regime`, `messageMaries`, covoiturage fields (`covoitVille`, `covoitPlaces`, `covoitTelephone`, `covoitEmail`, `covoitConsent`, `proposePlaces`), `rsvpSubmitted`, `successMessage`, `paymentInfo`, game state (`gameScore`, `gameLives`, `leaderboard`).

A real rebuild needs a proper backend/datastore for `GUESTS` and RSVP responses (currently client-side `localStorage`, fine for a prototype but not for a live multi-device wedding site — a guest's RSVP should persist across devices/browsers, and the couple needs a durable admin view).

## Files
- `source/index.html` — full design source (all markup/logic/copy/translations described above). Named `index.html` so it can be hosted directly (e.g. GitHub Pages serves this as the site root).
- `source/uploads/` — every real photo and image asset the design references, at the exact relative paths used in the HTML.
