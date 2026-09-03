# Omnibus-Type · Tools

Free, libre tools for type production and analysis, by [Omnibus-Type](https://www.omnibus-type.com).

This is a growing collection. Each tool is self-contained, needs no installation, and is free to use, study, and adapt. **TypeProof** is the first.

---

## TypeProof

A self-contained type testing page: load a font and inspect it across contexts and sizes, with live OpenType-feature and variable-axis controls. It is a single HTML file — open it in a browser. No server, no build step, no dependencies.

![TypeProof — Headlines, light theme](https://github.com/Omnibus-Type/Tools/TypeProof/screenshots/headlines.png)
*Headlines view, light theme.*

![TypeProof — Hamburgefonstiv, dark theme](https://github.com/Omnibus-Type/Tools/TypeProof/screenshots/hamburg.png)
*Hamburg view, dark theme.*

![TypeProof — a–z A–Z 0–9](https://github.com/Omnibus-Type/Tools/TypeProof/screenshots/alphabet.png)
*Alphabet and figures view.*

### Features

- Load `.ttf`, `.otf`, `.woff` or `.woff2` by drag-and-drop or file picker. Fonts are processed entirely in the browser — nothing is uploaded.
- Ten specimen views: Headlines, Text, adhesion, hamburg, a–z A–Z 0–9, Words, Caps, Diacritics, Kerning and Small sizes, each laid out as a proof sheet with the size marked in the margin.
- Editable specimens — click and type your own text; edits persist across views.
- Twelve OpenType feature toggles (`kern`, `liga`, `calt`, `dlig`, `smcp`, `onum`, `lnum`, `tnum`, `frac`, `zero`, `salt`, `ss01`).
- Variable-axis sliders read live from the font's `fvar` table, with each axis's real range.
- Tracking and leading controls; light and dark themes; adjustable paper and ink.
- Responsive down to phone width, with a draggable panel edge to trade controls for canvas.

### Use

Open [`type-proof/type-proof.html`](type-proof/type-proof.html) in any modern browser (Chrome, Firefox, Safari or Edge). Once the repository is published, the same file can be served through GitHub Pages and opened as a link.

### Credits

TypeProof continues the **Font Testing Page** by Pablo Impallari and Pablo Cosgaya, itself building on **Fontdrag** and Dave Crossland's **FontTest**. The interface is set in **Chivo** and **Chivo Mono**, Omnibus-Type's own typefaces, embedded in the file.

---

## Repository layout

```
Tools/
├── README.md
├── LICENSE
├── AUTHORS.md
└── type-proof/
    ├── type-proof.html
    └── screenshots/
        ├── headlines.png
        ├── hamburg.png
        └── alphabet.png
```

## Authors

Maintained by Omnibus-Type with the collaboration of Andrés Torresi. See [`AUTHORS.md`](AUTHORS.md).

## License

The tools in this repository are released under the **MIT License** — see [`LICENSE`](LICENSE). You may use, study, modify, and redistribute them freely.

The embedded typefaces **Chivo** and **Chivo Mono** are © Omnibus-Type and licensed separately under the **SIL Open Font License, Version 1.1**, not under the MIT license above.
