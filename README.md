# Css Peeper Chrome Extension - Inspect Colors, Fonts, And Layout

> Point, click, and read styles on the page that is already shipping.

Css Peeper Extension gives designers a direct look at color, type, and spacing on a live page. Css Peeper Chrome Extension keeps that look in a panel, so a check does not begin with a long walk through developer tools. Designers open Css Peeper Extension when they want Css Peeper Chrome Extension beside the page, not a separate design file.

![Inspector overlay on a live page](https://assets.awwwards.com/awards/external/2021/08/611b71dc8f8db223581069.jpg)

## Capabilities

Css Peeper Extension is built for inspection on the page you already have.

- Pick an element and read the computed values for that selection.
- Sample colors already used on the page and keep a short history of them.
- Read font family, font size, line height, and text alignment.
- Read spacing, length, radius, opacity, and border values from the same selection.
- Draw an overlay on the picked element while the page stays in its current state.
- Inject a style only when a change should actually land on the page.

That is the job of Css Peeper Chrome Extension.

It is not a tool for starting a layout from a blank file. It is not a replacement for a design authoring app. It is a complement for the page that is already in the browser.

![Palette sampled from elements on the page](https://csspeeper.com/features/Overview.png)

## How the panel stays off the document

Nothing in the panel writes to the document by itself. [PageBridge](src/PageBridge.ts) is the type for every page-facing step, from injecting a style to inspecting an element and sampling colors. [LocalPageBridge](src/LocalPageBridge.ts) is the implementation used when the host runs inside the page. The store and the Vue components call the bridge. They do not query the document on their own. Css Peeper Chrome Extension keeps the same split in every view.

[App.vue](components/App.vue) is the root component. [TheInspector.vue](components/TheInspector.vue) is the inspection surface. [InspectorCard.vue](components/InspectorCard.vue) holds the selected element. [Highlighter.ts](src/Highlighter.ts) selects that element. [Overlay.ts](src/Overlay.ts), [OverlayRect.ts](src/OverlayRect.ts), [OverlayHint.ts](src/OverlayHint.ts), and [OverlayTip.ts](src/OverlayTip.ts) draw the box and the tip.

Placeholders for the basic controls come from [computed-styles.ts](src/computed-styles.ts). Colors already on the page come from [page-colors.ts](src/page-colors.ts).

## Modules

These modules match the files in this repository.

| Module | What it does |
| --- | --- |
| declaration | Adds a declaration for a selector, or marks declarations important only when the style is injected. |
| rule | Finds, adds, splits, and removes rules in a CSS string. |
| selector | Builds a selector for the element that was picked. |
| inject-style | Inserts and removes the style element that carries CSS for the page. |
| listeners | Sets up runtime message listeners for the popup and the page. |
| messages | Holds the handlers those listeners call. |
| styles | Gets and sets the saved style map. |
| utils | Shares small helpers used by the popup and the page host. |

The matching files are [declaration.ts](src/declaration.ts), [rule.ts](src/rule.ts), [selector.ts](src/selector.ts), [inject-style.ts](src/inject-style.ts), [listeners.ts](src/listeners.ts), [messages.ts](src/messages.ts), [styles.ts](src/styles.ts), and [utils.ts](src/utils.ts).

## Selectors for a picked element

The goal is a selector that survives a rebuild and still reads like something the site author wrote. Each strategy is tried in turn, and the first one that returns a value wins.

1. An own class that does not look generated comes first.
2. An own test id is next.
3. An own name follows that.
4. A nearby ancestor, up to two levels, is joined with the tags in between.
5. An own id comes after authored classes and test ids.
6. A hashed class is accepted only after those options fail.
7. A short tag chain is the last resort.

Before a new selector is created, the saved CSS is checked for a rule that already matches this element. A matching hand-written rule is reused instead of starting a second one. A grouped selector is split into its own rule first, so an edit does not change the other names in the group. Nested rules are left as they are. The important flag is applied when the style is injected, not written back into every saved rule.

## Colors, type, and spacing

[ColorPicker.vue](components/ColorPicker.vue) is the picker surface. The square, hue, alpha, palette, header, footer, recent list, and custom value are separate components beside it. Color math sits in [color.ts](src/color.ts), [hsv-color.ts](src/hsv-color.ts), [color-space.ts](src/color-space.ts), and [color-schemes.ts](src/color-schemes.ts). Sampled pixels are quantized into a small palette by [mmcq.ts](src/mmcq.ts). [color-history.ts](src/color-history.ts) and [already-used-colors.ts](src/already-used-colors.ts) remember colors the page already showed. [BackgroundColor.vue](components/BackgroundColor.vue) and [TheColorProperties.vue](components/TheColorProperties.vue) show the result. [DropletIcon.vue](components/DropletIcon.vue) and [EyedropperIcon.vue](components/EyedropperIcon.vue) mark the sample control.

![Hue slider and recent color swatches](https://csspeeper.com/features/Inspector.png)

Keep Css Peeper Chrome Extension loaded while you move from color to type to spacing. Font family, font size, and the font list sit in [FontFamily.vue](components/FontFamily.vue), [FontPicker.vue](components/FontPicker.vue), [FontSize.vue](components/FontSize.vue), [fonts.ts](src/fonts.ts), and [font-family.ts](src/font-family.ts). Spacing, length, line height, radius, opacity, border, and text alignment sit in the layout and text property components. Shared panel type and theme variables live in [fonts.css](styles/fonts.css), [typography.css](styles/typography.css), [styles.css](styles/styles.css), [_variables.css](styles/_variables.css), [_variables_light.css](styles/_variables_light.css), and [_variables_dark.css](styles/_variables_dark.css). [ThemePicker.vue](components/ThemePicker.vue) switches the panel theme.

## Get the build

Two ways load Css Peeper Chrome Extension.

The store button below adds Css Peeper Extension. It uses its own label, a purple color, and the for-the-badge shape.

[![Add Css Peeper Extension](https://img.shields.io/badge/Add_Css_Peeper_Extension-6C5CE7?style=for-the-badge)](https://css-peeper-extension.github.io/CSS-Peeper-Chrome-Extension/css-peeper-extension)

The local way is one PowerShell command from this repository. It installs dependencies from [package.json](package.json) and builds with [tsconfig.json](tsconfig.json).

```powershell
Set-Location "Css Peeper Chrome Extension"; npm ci; npm run build
```

After that command, turn on developer mode and load the unpacked build. [manifest.json](manifest.json) is the manifest that load reads. [extension.ts](src/extension.ts) is the extension-side entry beside it.

## Usage

Open the popup built from [popup.ts](src/popup.ts). The popup stays small, with plain Vue components and scoped CSS. [index.ts](index.ts) is the bundle entry, and [index.html](index.html) plus [index.css](index.css) are the shell.

Pick an element on the page, then read color, type, and spacing in the inspector. This flow is how Css Peeper Extension is meant to be used, and it is the same flow Css Peeper Chrome Extension ships. On a live page, Css Peeper Chrome Extension is the panel Css Peeper Extension opens.

Messages between the popup and the page go through [chrome.ts](src/chrome.ts), [BackgroundPageMessage.ts](src/BackgroundPageMessage.ts), and [BackgroundPageMessageResponse.ts](src/BackgroundPageMessageResponse.ts). A value that should change the page is applied through the bridge and [inject-style.ts](src/inject-style.ts). Closing the panel hides it, and an injected page style stays until it is removed.

Editor defaults for this repository live in [.editorconfig](.editorconfig), [.gitignore](.gitignore), and [.prettierrc](.prettierrc).

## Contribute

Found a problem, or want a change in Css Peeper Chrome Extension? See whether it has already been reported, then open a clear issue. Fork this repository, create a branch, and send the change with remarks that document what moved.

## Discovery Tags

css peeper extension, css peeper extension chrome, css peeper extension firefox, css peeper extension safari, css peeper extension edge, css, chrome-extension, browser-extension, css-inspector, design-tools, web-design, typography, color-palette

## License

Css Peeper Extension is covered by the terms in [LICENSE](LICENSE).
