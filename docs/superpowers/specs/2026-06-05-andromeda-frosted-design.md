# Tokyo Nebula Andromeda Frosted — Design

Date: 2026-06-05
Status: Approved

## Goal

Add a sixth theme variant, `Tokyo Nebula Andromeda Frosted`, to
`themes/tokyo-nebula.json`: a copy of Andromeda that uses Zed's native
window transparency + blur, with no compromise on text clarity.

## Decisions made

- **Source of truth:** hand-author in this repo now; port the derivation
  rule into the `juanarrow-io/tokyo-nebula` build pipeline later. The
  variant is Zed-only (VS Code has no blur concept).
- **Frost layout:** frosted chrome, near-solid reading surfaces.
- **Terminal:** treated as a reading surface (near-solid), not chrome.

## Mechanism

The variant is bit-identical to Andromeda in every color choice. It differs
only in:

1. One new style key: `"background.appearance": "blurred"` — makes Zed
   enable native window blur with no user settings changes.
2. Alpha applied to surface colors per the frost rules below.

## Frost rules (the derivation)

| Tier | Keys | Alpha suffix |
|---|---|---|
| Chrome — frosted | `background`, `title_bar.background`, `title_bar.inactive_background`, `tab_bar.background`, `tab.inactive_background`, `status_bar.background`, `toolbar.background`, `panel.background`, `surface.background` | `cc` (80%) |
| Reading — near-solid | `editor.background`, `editor.gutter.background`, `editor.subheader.background`, `tab.active_background`, `terminal.background`, `scrollbar.track.background` | `f2` (95%) |
| Untouched — opaque | `elevated_surface.background` (menus, completions, hovers), all text/syntax/icon colors, borders, diagnostics `*.background` status colors, ANSI palette | — |

Notes:

- Keys that already carry alpha (selections, search matches, hover/element
  states, scrollbar thumb) are left unchanged; they composite on top of the
  surfaces.
- Borders stay solid `#0a0c12` so pane separation remains crisp.
- `scrollbar.track.background` gets the editor alpha so it does not paint a
  solid seam over the frosted editor edge.

## Readability stance

Text, syntax, and popovers are identical to Andromeda and fully opaque. The
only contrast risk is the 80% chrome, which holds only muted secondary
text. If the visual check shows chrome text suffering over a bright
wallpaper, the tuning knob is the chrome alpha (`cc` → `d9`), never the
text colors — the variant stays a pure derivation of Andromeda.

## Implementation

- Generate the variant with an inline one-off script: copy the Andromeda
  theme object, apply the alpha map, insert after Andromeda in the `themes`
  array. No permanent helper scripts.
- Update the README variant list and `extension.toml` description
  ("five-variant" → "six-variant").

## Verification

- `themes/tokyo-nebula.json` parses as valid JSON; the new entry has the
  same key set as Andromeda plus `background.appearance`.
- Real gate: install via `zed: install dev extension`, select the theme,
  eyeball against a bright/busy wallpaper. Blur cannot be asserted in a
  test.

## Platform note

Blur is macOS-native. On Linux it depends on the compositor and degrades
gracefully to transparency or opaque. Nothing to handle in the theme.
