---
status: paused
current_phase: appearance legibility
---

# Theme-aware window chrome

## Outcome

Changing the theme updates the document window header and native tab bar along with the editor. This covers Tomorrow Night Blue, Catppuccin, and Palenight, plus Light/Dark/Match System. Native buttons, labels and subtitles, tab titles, the tab lifecycle, and document and tab restoration must all be preserved.

## Current

Header theming landed: `NavigationBar` fills with the text view's theme background (`OutlineNavigator.backgroundColor`). Its fill extends under the transparent full-size title bar, so it paints the header and tab strip. Mechanism and constraints: fork `README.md`. Verified with a probe build on macOS 27.2: live switching between the three families, intact buttons, title, and toolbar, Merge All Windows with label propagation, and native tabs (confirmed visually by the user).

Open defect: in Light mode, or Match System with a light system, a dark theme without a light variant (e.g. Tomorrow Night Blue) gets light-appearance chrome over a dark header. The title text is illegible and the toolbar platters render light. Intended fix: the window appearance follows the theme's darkness only when the theme cannot follow the appearance, meaning `pinsThemeAppearance` is set or the theme has no counterpart variant. Otherwise the effectiveAppearance observer in `DocumentViewController` stops toggling paired themes.

Unverified: tab detach, close, and switch in detail; a hidden navigation bar; window opacity below 1; fullscreen; normal relaunch with restoration.

Next: implement the appearance rule in one place and verify it in the probe under Light, Dark, and Match System with paired and single-variant themes. Then run the unverified items on one Release build before installing.

## Evidence and method

- Rejected approaches: `titlebarAppearsTransparent`, removing `.fullSizeContentView`, and painting `window.backgroundColor` or `HoleContentView`. They blanked the header or broke tab grouping, and `window.backgroundColor` does not paint the full-size header.
- Probe: stage a Debug build with `PRODUCT_BUNDLE_IDENTIFIER=com.autoflolabs.ChromeProbe`. Then rename it (`CFBundleName` "ZZ PROBE"), remove the icon keys, and re-sign ad hoc with the original entitlements. Never override `PRODUCT_NAME`. Use synthetic documents only. Drive menus through System Events by PID; this works without activating the app, except for tab actions, which need a main window. Capture with `screencapture -l <windowID>`, which does not reliably show the tab bar. Inspect views read-only with `lldb -p <pid> --batch` and `_subtreeDescription`.
- User content typed into the earlier test app is backed up in `~/Documents/CotEditor-recovered-2026-09-29/`.
