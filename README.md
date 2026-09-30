# CotEditor

CotEditor is a lightweight plain text editor designed for macOS. The project aims to provide a general plain text editor for everyone with an intuitive macOS-native user interface.

## Customized fork

The active fork is **[ZachLevein/CotEditor](https://github.com/ZachLevein/CotEditor)** (`origin`). `upstream` is [coteditor/CotEditor](https://github.com/coteditor/CotEditor). The AutoFloLabs legacy repository is retired; do not use it for builds or publication. This fork is based on 7.0.7, with `beta` for integration and `main` for separately authorized promotion.

- **Themes** is a top-level menu: select a theme family, use **Cycle Theme (⌥⌘K)**, or choose **Match System / Light / Dark**. Checkmarks reflect the app's saved preferences. Light/dark pairs are grouped under one name; a theme with only one variant uses that available variant.
- The window header and native tab bar take the theme's background color. It is painted by the navigation bar background, which extends under the transparent full-size title bar. Do not change title-bar transparency or `.fullSizeContentView` for this: those experiments hid the window buttons, labels, and tabs.
- Theme changes run inside the app through `ThemeManager`, including user-installed themes. They do not invoke shell scripts or require Full Disk Access. The old generated **Script → Themes** group is hidden when its `_theme-lib.sh` marker is present; other scripts are unaffected. Do not reinstall the old theme-menu scripts to configure this menu.
- **View → Window → Label Window…**, or the title-bar context menu, sets a label shared by a tab group. The label appears as the title and the active document as its subtitle, and survives relaunch.

### Menu layout

The menu bar is **CotEditor · File · Edit · Format · View · Themes · Script** (Script uses its icon). Secondary command groups are nested:

| Commands | Location |
|---|---|
| Find & Replace, navigation | **Edit → Find**; **⌘F** still opens Find & Replace |
| Text transformations, snippets, indentation | **Format → Text** |
| Window management and labels | **View → Window** |
| Help | **CotEditor → Help** |

The original actions and shortcuts are retained. Quit remains the final command in the CotEditor menu.

### Working on this fork

Use an isolated topic based on current `beta`; land verified work onto `beta`. Publishing a release or promoting to `main` is a separate operation. The local gmux workspace name is `autofloeditor`; `coteditor` names the separate configuration repository.

The menu is built in `CotEditor/Sources/Application/AppDelegate.swift`; `organizeMainMenu()` reparents existing menu items using outlets from `CotEditor/Storyboards/Base.lproj/Main.storyboard`. Preserve their system-menu identities and actions rather than duplicating commands. Family/appearance resolution belongs to `CotEditor/Sources/Setting Managers/ThemeManager.swift`. Legacy script-group suppression belongs to `ScriptManager.swift` in the same directory. Keep one theme list and the existing app preferences as the source of truth.

For window-label changes, never access `window.tab` during window placement: it creates a tab object and can prevent tab grouping. Defer tab-group inspection outside `windowDidBecomeMain`; show the title-bar context menu on mouse-up. Verify New Tab, joining/merging tab groups, labels, and relaunch after changes in that area.

## Upstream project

- __Requirement__: macOS Sequoia 15 or later
- __Web Site__: <https://coteditor.com>
- __Mac App Store__: <https://apps.apple.com/app/coteditor/id1024640650>
- __Languages__: English, Bulgarian, Simplified Chinese, Traditional Chinese, Chinese (Hong Kong), Czech, Dutch, English (UK), French, German, Italian, Japanese, Korean, Polish, Portuguese, Russian, Spanish, and Turkish

![screenshot](screenshot@2x.png)


## Design Philosophy

CotEditor is built with a clear focus on being a truly __macOS-native__ text editor.
Its design emphasizes the following principles:

- __Behave as a first-class macOS application.__
  CotEditor adopts system-native UI components, conventions, and behaviors so that it feels instantly familiar to macOS users. Rather than asserting its own personality, CotEditor aims to blend naturally into the macOS experience as one of its native apps. Features that deviate from standard macOS behavior may be rejected, even if they’re common in other editors.

- __Be accessible and comfortable for both beginners and advanced users.__
  CotEditor aims to stay simple enough for casual use while providing the precision and control expected by experienced editors and developers.

- __Less is more.__
  CotEditor avoids unnecessary complexity, as minor options accumulate and ultimately place unnecessary decision-making burdens on users.

- __Handle a wide range of plain text formats accurately.__
  From everyday notes to niche or legacy formats, CotEditor prioritizes correct text handling, encoding support, and predictable editing behavior.

- __Respect a diverse user base through localization and accessibility.__
  Whenever possible, CotEditor integrates macOS features for localization, accessibility, and user customization to serve a global audience.

These principles guide the project’s long-term direction and day-to-day development decisions,
and they also help determine which feature requests align with CotEditor’s macOS-native identity.



## Source Code

CotEditor is a purely macOS native application written in Swift. It adheres to Cocoa's document-based application architecture and respects the power of `NSTextView` and related text system APIs.


### Development Environment

- macOS Tahoe 26
- Xcode 26.5
- Sandbox and hardened runtime enabled



## Contribution

CotEditor has its own contributing guidelines. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before creating an issue or submitting a pull request.



## How to Build

### Build for ad‑hoc usage

Ad-hoc signing is already selected in `Configurations/CodeSigning.xcconfig`. From the source checkout:

```sh
xcodebuild -project CotEditor.xcodeproj -scheme CotEditor \
  -configuration Release -derivedDataPath build \
  -destination 'platform=macOS,arch=arm64' \
  -skipPackagePluginValidation -skipMacroValidation build
codesign --verify --deep --strict build/Build/Products/Release/CotEditor.app
```

Before installing, quit CotEditor normally and let it preserve open documents; do not force-quit or discard unsaved work. Then install and launch:

```sh
ditto build/Build/Products/Release/CotEditor.app /Applications/CotEditor.app
codesign --verify --deep --strict /Applications/CotEditor.app
open /Applications/CotEditor.app
```

Verify the actual installed app: the menu bar and nested groups match the layout above, **⌘F** opens Find & Replace, and Text has a visible title; Themes is top-level with no generated duplicate under Script; theme selection, Light/Dark/Match System, and cycling work; existing documents and window labels restore. A shell test alone does not verify a menu action. Updating `beta` does not replace the installed app.


## License

© 2005-2009 nakamuxu,
© 2011, 2014 usami-k,
© 2013-2026 1024jp.

The source code is licensed under the terms of the __Apache License, Version 2.0__. Image resources are licensed under the [__Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License__](https://creativecommons.org/licenses/by-nc-nd/4.0/). See [LICENSE](LICENSE) for details.
