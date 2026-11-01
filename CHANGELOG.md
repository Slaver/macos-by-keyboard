- # v.1 (21.12.2020)
- Initial release

- # v.2 (21.12.2020)
- Ability to switch layots with CapsLock
- Keyboard installer

- # v.3 (23.12.2020)
- Belarusian - PC layout
- New, more default-looking icon
- Removed trash from bundle (duplicated ABC layout)
- Layout name locales for be and ru

- # v.4 (27.09.2021)
- Fixed caps/option modificators for PC layout

- # v.5 (30.07.2026)
- Replaced flag icon with plain "BE" letters, matching the native macOS Tahoe input source style

- # v.6 (30.07.2026)
- Redrew the "BE" icon in SF Compact (system font) to match built-in Tahoe input source badges pixel-for-pixel in style

- # v.7 (04.08.2026)
- Removed custom input source icons so macOS Tahoe uses its native language indicator

- # v.8 (04.08.2026)
- Restored a template letter icon because Tahoe shows a generic keyboard for third-party layouts without an icon
- Changed the bundle and input source identifiers to avoid the cached legacy flag

- # v.9 (04.08.2026)
- Changed the letter icon to a lighter Regular weight
- Converted the icon to a strictly black-and-transparent binary template so Tahoe can invert it on the active background

- # v.10 (05.08.2026)
- Replaced the multi-resolution palette ICNS that crashed Tahoe's CursorUIViewService with a validated standard 16×16 RGBA ICNS
- Added a rounded template frame to approximate Tahoe's system-generated input source badge
- Added WRW to localized layout names so the custom layout is distinguishable from macOS's built-in Byelorussian source

- # v.11 (05.08.2026)
- Changed the internal keyboard names and numeric layout IDs, which are the legacy HIToolbox registration keys, to prevent reuse of the cached flag icon

- # v.12 (13.08.2026)
- Kept only the Belarusian PC layout and removed duplicate standalone resources and the standard macOS-style layout
- Replaced the pixelated keyboard-frame icon with a multi-resolution `БЕ` template badge using the Tahoe system label and antialiased SF typography
- Added fresh stable bundle, input-source, layout-name, and numeric IDs so Tahoe does not reuse the old flag cache
- Rebuilt the DMG with the single clean layout bundle

- # v.13 (13.08.2026)
- Fixed the small (16/32px) icon representations, which contained corrupted noise pixels instead of the `БЕ` glyph, making the icon look garbled in the Input Sources picker
- Redrew the icon as plain bold `БЕ` letters with generous padding and no background shape, so it stays legible at menu bar size
- Rebuilt the DMG

- # v.14 (14.08.2026)
- Root-caused the broken indicator by reverse-engineering Tahoe's input source machinery (HIToolbox, KeyboardLayouts.framework): the `TISInputSourceID` override inside `KLInfo_Belarusian PC` made Tahoe register a second, legacy input source that carries the *generic keyboard* icon instead of the bundled `.icns` — that legacy source is what got enabled, producing the empty rectangle in the menu bar and the keyboard glyph in the switcher
- Removed the `TISInputSourceID` override so only the modern auto-registration remains (`by.wrw.keyboardlayout.belarusian-pc.keylayout.BelarusianPC`), which correctly picks up `Belarusian PC.icns`
- Fixed the icon generator rendering at 2x Retina scale regardless of requested size: a true 16×16 was never produced and `iconutil` silently dropped small-size slots, so the menu bar had nothing to draw; all 10 icns slots (16…1024 + @2x) are now real, correctly-sized renders
- Discovered that Tahoe ignores `TISIconIsTemplate` for third-party keylayouts (gated by a flags bit in the HIToolbox layout cache), so no automatic light/dark inversion is possible; recolored the `БЕ` glyph to the system badge gray, legible on both light and dark menu bars
- The native wide text badge (like Apple's own `БЕ`) is confirmed impossible for third parties: Tahoe generates it from a hardcoded 278-entry table keyed by `com.apple.*` input source IDs
- Rebuilt the DMG; on update, log out and back in and re-add the layout so the stale legacy source disappears
- Known transitional issue: until that logout, apps that were already running may crash when the layout switcher HUD is opened via hotkey — Tahoe's icon manager does `CFRelease(TISCopyInputSourceForID(...))` without a NULL check, and long-running processes still reference the removed legacy input source ID; fresh processes after re-login are unaffected

- # v.15 (14.08.2026)
- Redrew the indicator as a rounded-rect frame with bold condensed `БЕ` inside, so it matches the built-in Tahoe badges instead of floating bare letters; the geometry (1pt stroke, 4pt corner radius, stroke flush with the canvas edge) was measured pixel-by-pixel from the system's own badge
- The custom badge stays square (16×16) while Apple's are 22×16, because the wide format comes from a system table keyed by `com.apple.*` input source IDs; the letters use a condensed weight to stay legible in the narrower frame
- Established that the icon cannot follow the highlight colour in the input switcher: `TISIconIsTemplate` is read correctly (HIToolbox passes `kCFBooleanTrue` into the icon builder), but `islCreateKeyboardLayoutIconImage` unconditionally writes `kCFBooleanFalse` back into `TSMInputSourcePropertyIconShouldBeTemplate` right after materialising the icon, so `-[KLInputSourceIconManager indicatorImageForInputSource:]` draws the icon literally instead of inverting it — a Tahoe bug no bundle setting can work around
- The menu bar is unaffected by that bug because it uses the icon `NSImage`, which keeps `isTemplate = YES`, so the badge still inverts for light and dark menu bars; the ink is a mid-gray chosen to stay readable in the switcher's non-inverting case as well
- Rebuilt the DMG

- # v.16 (19.08.2026)
- Recolored the badge from mid-gray to near-black, because the input switcher draws the icon literally: on the light switcher panel and on the blue selected row the gray had almost no contrast, while near-black is the highest-contrast choice on both (black-on-accent-blue actually beats white-on-accent-blue)
- The menu bar is unaffected by the ink colour: it draws the icon as a template, so only the alpha channel matters there and the badge still follows light and dark menu bars
- Established that Apple's own layouts use *two different* icons: the menu bar badge is a 22×16 rounded-rect frame (with a filled variant for the highlighted state), while the input switcher indicator is the bare letters with no frame — a third-party bundle can only supply one image, so the framed badge is used in both places
- Rebuilt the DMG

- # v.17 (22.08.2026)
- Redrew the indicator as a solid rounded keycap with the `БЕ` letters knocked out of it (transparent), replacing the outlined frame with near-black letters from v16
- Reason: the system appearance here is set to switch between light and dark, and the input switcher draws third-party icons literally (the `TISIconIsTemplate` bug), so any fixed ink colour fails in one of the two appearances — near-black was invisible on the dark switcher panel, near-white would be invisible on the light one
- The knocked-out design has no fixed letter colour at all: the letters show whatever is behind them, so they read on the dark panel, on the light panel and on the blue selected row alike — which is exactly how Apple's own highlighted badge works
- The keycap is a light gray fill (0.80) with a darker border (0.35) so the badge still has a visible edge on white backgrounds
- Knocking the letters out is also what keeps the menu bar working: the menu bar consumes the icon as a template and only reads the alpha channel, so the letters must be holes rather than dark pixels to stay visible there
- Verified the design by rendering every icns slot against the real backgrounds (dark panel, accent blue, light panel, white, black) instead of reasoning about contrast in the abstract
- Confirmed by probing `KLInputSourceIconManager` that Apple's switcher indicator really is bare letters with no frame or chip — the light chip seen behind Apple's letters in the switcher is drawn by the switcher itself, and only for template icons, which is why a third-party icon has to draw its own
- Rebuilt the DMG

- # v.18 (01.11.2026)
- Added typographic characters to the Option layer of the Belarusian PC layout: ⌥`-` → `—`, ⇧⌥`-` → `–`, ⌥`б` → `«`, ⌥`ю` → `»`, ⇧⌥`б` → `„`, ⇧⌥`ю` → `“`, ⌥`г` → `ґ`, ⇧⌥`г` → `Ґ`
- ⌥`-` and ⇧⌥`-` are swapped relative to before (short dash was on ⌥, long on ⇧⌥); `≤`, `≥`, `ѓ` and `Ѓ` are no longer on the Option layer
- Put on Option rather than Command because macOS does not insert text for ⌘ combinations (they are routed to menu shortcuts), so the ⌘ layer cannot produce typed characters; ⌘ keeps its original shortcut behaviour
- Rebuilt the DMG
