# Handoff: Site de Mariage — Léa & Pierre-Alexandre

## Overview
Site privé de mariage (invités connectés par code d'invitation) pour Léa & Pierre-Alexandre, mariage le 14 août 2027 au Domaine de Meaucé (Le Perche). Une seule page longue défilante avec sections: accueil, save-the-date, histoire du couple, programme, dress code (jeu d'habillage avec vraies photos), infos pratiques, domaine/hébergement, RSVP, mini-jeu 3D, FAQ, galerie, contact. Inclut un back-office admin simple.

## About the Design Files
The bundled files are **design references built as an HTML prototype** (inline-styled, single-file "Design Component" runtime specific to our design tool — NOT a framework you should copy verbatim). Treat them as the visual/interaction spec. Recreate the design in whatever stack the target project uses (plain HTML/CSS/JS is perfectly fine here — no framework is required by the design itself). If no project exists yet, a static site (HTML/CSS/vanilla JS, or a lightweight framework like Astro/Next if you want routing/build tooling) is the natural fit — this is a small marketing/RSVP site, not an app.

## Fidelity
**High-fidelity.** Colors, type, spacing, copy (in French) and animation behavior described below should be recreated as closely as possible. The three.js runner game and the photo-based dress-up game are functional prototypes — reproduce their mechanics, not just a screenshot.

## Global Design System

### Colors
- Ink (primary text/dark bg): `#1a1714`
- Cream (page background): `#faf5ec`
- Warm sand (alt section bg): `#efe4d0`
- Antique gold (accent): `#d9b78c` (brighter gold accent2: `#c9a769`)
- Burgundy (accent/CTA): `#7a2e35`
- Sage green (secondary accent): `#7d8c6c`
- Off-white (dark-bg text): `#fffdf8`
- Error/soft red: `#e29a94`
- Selection highlight: background `#7a2e35`, text `#fffdf8`

Section backgrounds alternate between `#faf5ec` (cream), `#efe4d0` (sand), and `#1a1714` (ink, used for Domaine, Jeu, Contact, Footer) to create rhythm — max 3 backgrounds total, no more.

### Typography
- Display/headers: **Fraunces** (variable, opsz 9–144, weight 300–700, italic supported). Headlines use italic weight for accents (e.g. the "&" between names) and high optical size (opsz 144) for large display type.
- Body/UI: **Inter** (weight 200–600).
- Google Fonts import: `Fraunces:opsz,wght,ital@9..144,300..700,0..1` + `Inter:wght@200..600`.
- Uppercase labels use `letter-spacing: .08em–.2em`, 11–15px, often in gold `#d9b78c` or ink depending on background.
- Large display numbers (date "14/08/2027", countdown) use Fraunces at very large sizes (clamp 56px–190px) with opsz 144.

### Motion
- Reduced-motion media query disables all animation durations globally — keep this.
- Scroll-linked reveal: sections fade/translate in on scroll (via IntersectionObserver-style refs, `reveal.<section>` state).
- Hero: subtle parallax on mouse move (`heroParallaxStyle`, driven by mouseX/mouseY state).
- Save-the-date section: sticky scroll section (130vh wrapper, 100vh sticky inner) with date zooming/fading in as user scrolls past (`dateFlightStyle`) — replaced an earlier 3D flip-card approach with softer fade/zoom.
- Marquee: continuous horizontal auto-scroll ticker (duplicate content, translateX -50%, 40s linear infinite) with couple names/date/venue.
- Deadline banner: shimmering gold-to-white text gradient sweep (background-position animation, 3.5s linear infinite).
- Nav: hides/shows and adds background on scroll (`navStyle`/`navHidden` state).
- Scroll progress bar: fixed 3px top bar, gradient burgundy→gold, width tied to scroll %.

## Screens / Sections (in page order)

### 1. Login gate
Full-screen photo background (image placeholder "photo · vous deux") with dark gradient overlay (top 35% opacity → bottom 94%). Centered monogram "L • PA" top. Bottom-anchored content: couple name headline (Fraunces italic "&"), date/venue line in gold uppercase, then an invitation-code input (bottom-border-only style, centered, uppercase, letter-spaced) + error message state ("Code non reconnu, vérifie ton faire-part.") + full-width pill CTA "Entrer sur le site" (gold bg, ink text, hover → cream bg).

### 2. Witness message overlay (modal)
Full-screen dark blur overlay triggered by state (`showTemoinMsg`), shows a personal message ("Un mot pour toi") in large italic Fraunces, gold label, outline button "Continuer" to dismiss. Used to deliver a personalized note to specific guests (e.g. witnesses) after login.

### 3. Nav bar
Sticky/fixed, appears after scroll. Left: "Léa & PA" italic wordmark. Center links: Histoire / Programme / Infos / Jeu / FAQ (uppercase, 13px, letter-spaced, no underline). Right: "Confirmer ma présence" pill button (ink bg/cream text) linking to #rsvp, plus a small circular logout button (⏻ icon, outline).

### 4. Deadline banner
Full-width ink bar under nav: "Merci de répondre avant le 20 mai 2027 · [dynamic countdown label]" in shimmering gold gradient text.

### 5. Hero
100vh, full-bleed photo crossfade background (4 placeholder photos cycling), radial dark vignette overlay. Centered content: small italic "Nous nous marions" label, huge display headline built letter-by-letter (animated stagger reveal) "Léa & Pierre-Alexandre", venue/date line, and a 4-up countdown (days/heures/min/sec) in large Fraunces numerals with small uppercase labels underneath.

### 6. Marquee ticker
Ink background strip, gold italic scrolling text repeating "Léa & Pierre-Alexandre · 14.08.2027 · Domaine de Meaucé · Le Perche".

### 7. Save the date (sticky reveal)
130vh scroll container, 100vh sticky centered content. Cream background. Small italic burgundy eyebrow "Réservez la date", giant date "14 / 08 / 2027" (slashes colored sage then gold), thin burgundy divider line beneath. Animates in via scroll-tied opacity/scale (`dateFlightStyle`) — deliberately soft fade/zoom, not a 3D flip.

### 8. Histoire (our story)
`id="histoire"`, cream bg, centered heading + reveal-on-scroll body content (timeline or narrative blocks — read full file for exact copy/layout if needed, this section was stable/unchanged in recent iterations).

### 9. Memory bubbles (sticky)
190vh scroll container with 100vh sticky center — floating photo "bubbles" (circular image placeholders) that animate in as the user scrolls, likely a montage/gallery-teaser moment between story and programme.

### 10. Programme
`id="programme"`, cream bg. Lists ceremony events as **distinct event groups**: civil (mairie), accueil, cocktail, dîner — each its own card/block with time, location, and a **redesigned calendar-style date button** plus **GPS action buttons** (Google Maps / Apple Maps / Waze) that deep-link to the venue per event. No food/menu mentions (deliberately removed).

### 11. Dress code — Photo Paperdoll Game
Sand bg (`#efe4d0`). Interactive dress-up game: guest uploads real photos of themselves + partner via `<image-slot>`-style upload, then can apply outfit style presets — **Romantique / Chic / Champêtre** — which overlay clothing graphics onto the real photo while preserving the real face (photo composite, not a drawn avatar). This replaced an earlier flat illustrated-mannequin version. Purpose: helps guests visualize dress-code options for themselves.

### 12. Infos pratiques
`id="infos"`, cream bg. Practical info cards — no navigation/route hints included (previously had "A11" highway references, now removed; assume guests use their own GPS/the programme's map buttons).

### 13. Domaine (venue)
Ink bg, 2-col grid layout. Focuses on **venue amenities**: pool, indoor halls/rooms, weather-contingency plan (indoor backup for rain). Includes working link(s) to the venue.

### 14. Hébergement sur place (on-site lodging)
Sand bg — accommodation options at the venue itself.

### 15. Gîtes à proximité (nearby lodging)
Cream bg — nearby off-site accommodation options/links.

### 16. RSVP
Sand bg, `id="rsvp"`. Form separated into the same **event groups as Programme** (civil / accueil / cocktail / dîner) so guests can confirm attendance per event rather than as one blanket yes/no. Includes calendar-add buttons (styling redesigned to match Programme's calendar buttons).

### 17. Jeu (mini-game)
Ink bg, `id="jeu"`. **Three.js 3D endless-runner** — third-person chase camera, textured procedural grass ground with road-stripe markers receding into fog, low-poly rolling hills and trees flanking the road, two low-poly character models (bride/groom, distinguishable by cone-vs-cylinder torso, hair shape, skin/outfit colors) as the runnable avatar(s), atmospheric fog (`THREE.Fog`, 0xf2dfb8, near 12 / far 40), soft shadows (PCFSoftShadowMap), ACES tone mapping. Style target given by the user: "Subway Surfers / GTA" arcade third-person runner rather than a flat canvas game. Rendered into a fixed-size WebGL canvas (640×420) mounted inside the section. Obstacles use cylinder geometry; ground shadow-catcher plane under the character group. Game loop/physics/lane-switching logic lives in `componentDidMount` — see source lines ~1130–1400+ for full mechanics (character rig, obstacle spawn/movement, collision, scoring) before reimplementing.

### 18. FAQ
`id="faq"`, cream bg — accordion or list of Q&A (check source for exact copy).

### 19. Galerie
Sand bg — photo grid; uses `aspect-ratio` (not fixed height) on grid items so it collapses correctly on mobile (this was an explicit mobile bug fix — preserve the aspect-ratio approach, don't revert to fixed px heights).

### 20. Contact
Ink bg, centered — "Une question ?" prompt with contact method(s).

### 21. Footer
Ink bg, 60% opacity, small text: "Léa et PA · 14 août 2027 · Domaine de Meaucé" + an "Admin" text link that opens the admin overlay.

### 22. Admin overlay
Full-screen overlay (`z-index:3000`, cream bg) opened via footer "Admin" link — simple back-office, likely for managing invite codes / viewing RSVP responses (inspect `adminOpen` state block in source for exact fields before rebuilding).

## Interactions & Behavior
- **Auth gate**: `codeInput` state → `onLoginSubmit` validates against invite codes; wrong code sets `loginError` true and shows inline message; correct code sets `loggedIn` true and may set `showTemoinMsg`/`temoinMessage` for personalized guests (e.g. witnesses).
- **Logout**: circular nav button clears `loggedIn`.
- **Scroll reveal**: each major section has a `ref` registered in `regReveal.<name>` and a computed `reveal.<name>` style (opacity/transform) — implement via IntersectionObserver in the rebuild.
- **Countdown**: live-updating days/hours/minutes/seconds to 14 Aug 2027, and a separate RSVP-deadline countdown label (20 May 2027) in the banner.
- **Dress-up game**: user uploads 2 photos (self + partner) → selects a style preset (Romantique/Chic/Champêtre) → outfit overlay graphics composite onto the photos, face untouched.
- **3D runner game**: keyboard/touch controls to move character between lanes, avoid obstacles scrolling toward camera down an infinite road, score increments with distance/survival.
- **RSVP**: per-event-group attendance toggles (civil/accueil/cocktail/dîner), plus "add to calendar" buttons per event.
- **GPS buttons** (Programme section): each event has 3 buttons opening Google Maps / Apple Maps / Waze with the venue's coordinates/address pre-filled.
- **Admin**: opened by footer link, presumably CRUD over invite codes/RSVPs — confirm exact scope from source before building a real backend for it.

## State Management (from the prototype's state object)
Representative keys (see `state = {...}` around line 913 of the source for the authoritative list): `loggedIn`, `codeInput`, `loginError`, `code`, `showTemoinMsg`, `temoinMessage`, `navHidden`, `scrollProgress`, `mouseX`/`mouseY`, `cx`/`cy` (parallax), `countdown` (days/hours/minutes/seconds), `rsvpCountdownLabel`, `heroPhotos`, `reveal.*` / `regReveal.*` per section, `adminOpen`.

A real rebuild needs: auth/session state, per-guest invite-code → RSVP-status data model (ideally backed by a real datastore, not client-side only, since RSVP responses must persist), per-event RSVP responses, and photo uploads for the dress-up game (needs file storage).

## Assets
- Fonts: Google Fonts — Fraunces, Inter (loaded via `fonts.googleapis.com`).
- Photos: all currently **placeholders** (`<image-slot>` drag-and-drop placeholders) — hero photos, login background, memory bubbles, gallery, and the two dress-up-game portraits are all awaiting real photos of Léa & Pierre-Alexandre from the couple. Replace with real images before launch.
- 3D game: geometry/materials are procedurally generated in Three.js (no external 3D model files) — canvas-based grass texture, low-poly primitive character rigs. r128 of three.js loaded via cdnjs.

## Files
- `source/Mariage Lea PA.dc.html` — primary design source (all markup/logic described above).
- `source/Mariage Lea et Pierre-Alexandre - standalone.html` — self-contained export of the same design (no external build step needed to view).
Both are bundled in this handoff folder for reference; treat them as the spec to recreate, not code to import as-is.
