# FontLab Online · Unicode Font Comparison

**Compare Unicode glyphs, character mappings and browser font loading without installing fonts.**

[Open the tool](https://fontlab-online.netlify.app/) · [中文说明](README.md) · [Font licenses and credits](https://fontlab-online.netlify.app/licenses.html) · [Report an issue](https://github.com/vryTfmcat/fontlab-online/issues)

FontLab Online is a static web tool for comparing text across bundled fonts and font files you select from your computer. It separates **character mappings, file loading, pixel comparisons and confidence** instead of treating every visible fallback glyph as support from the selected font.

The interface is currently in Chinese. This guide explains the main controls and results.

## Who is it for?

- People working with IPA, mathematical symbols, rare characters or multilingual notes.
- Unicode and historical-script enthusiasts comparing glyph shapes.
- Obsidian theme users and web developers testing font files before installing them.
- Font users who need to distinguish a missing mapping from a loading or rendering problem.

Try pasting:

```text
汉字 仮名 𒀀 𝔸 → ✓ ∑
ɑ ɛ ə ʃ ʒ ŋ sœk˧
A À À
```

The final line includes a precomposed character and a combining sequence. Individual code points are listed separately; verify complete sequence layout in your target application.

## Quick start

1. **[Open FontLab Online](https://fontlab-online.netlify.app/)** and paste text into **输入文字** (“Input text”), or choose a sample.
2. Select fonts under **选择字体** (“Select fonts”). Adjust **字号** (“Font size”) and compare the previews, fallback reference and code-point table.
3. To test your own files, use **本地导入字体** (“Import local fonts”) and its file picker, or drag files into that area. Click **导出本次判定结果 JSON** (“Export diagnostic JSON”) to save the results.

There is no account requirement and no need to install fonts or run a local server.

The site bundles **12 font files**, including Gentium styles in TTF and WOFF2, Noto Sans Symbols2, Noto Sans Cuneiform, Unifont and Unifont Upper. These are not 12 distinct families: styles and formats are separate entries. Bundled font files load only when selected.

## Reading the results

| Chinese label | Meaning |
| --- | --- |
| 本字体覆盖（确定） | A mapping exists and the current rendering differs from the fallback reference, providing evidence of rendering from the selected font. |
| 空白字符·字体已定义 | A whitespace character has a mapping. It is handled separately from visible-glyph coverage. |
| 像素与回退相同·未能区分 | Pixels do not distinguish the results. This is not proof of either missing support or a visible glyph from this font. |
| cmap 无映射 | The parsed mapping has no entry for this code point. Seeing a character does not establish support from this font. |
| 有映射但描不出可见字形 | A mapping exists, but the current drawing produces no visible glyph. This is separate from a missing mapping. |
| 字体文件无法加载 | The browser could not load the file. Fallback text is not counted as successful font loading. |
| 映射未解析 | The mapping is unknown because it was not parsed; unknown does not mean absent. |

Bundled fonts use precomputed data from the corresponding files. Imported fonts are processed independently: a matching filename never substitutes bundled mapping data for the imported file. JSON exports identify these sources.

## Local files, privacy and limitations

- The picker accepts **TTF, OTF, TTC, WOFF and WOFF2**. Actual parsing and rendering depend on the file and browser. File reading, parsing, hashing and previews happen in the browser; the application has no font-upload endpoint.
- **Imported WOFF2 files can currently be previewed, but their cmap tables are not parsed.** Their mapping state remains explicitly unknown. Use a parseable TTF or OTF when you need mapping information. Bundled WOFF2 files have precomputed mapping data.
- The browser may reject incompatible font structures. The web version does not repair files or automatically substitute compatibility copies.
- Comparisons are made per Unicode code point in the current browser. They are not complete tests of combining sequences, ligatures, complex-script shaping or cross-browser layout.
- The tool does not inspect arbitrary websites to identify their active fonts, and it does not identify fonts from images.
- Exported JSON includes the input text, filenames and diagnostic results. Review it before sharing.
- Loading the site and its bundled fonts requires network access. Keeping imported files local does not make the whole site an offline application.

## Font licenses

Bundled font files are distributed unchanged. Per-file notices, credits and license texts are available on the **[licenses page](https://fontlab-online.netlify.app/licenses.html)** and in [licenses/](licenses/).

Other source fonts and compatibility copies are not bundled. Importing a file does not grant permission to redistribute it; the site does not determine licensing for user-selected files. Font licenses apply to their respective font files and should not be assumed to license the application code.

## Feedback

Please [open an issue](https://github.com/vryTfmcat/fontlab-online/issues) with:

- Browser and operating system.
- Reproduction text, font size and steps.
- Font name and format, and whether it is bundled or imported.
- Expected versus actual behavior; optionally a screenshot or a reviewed JSON export.

You do not need to upload a private or restricted font. A source link, version information or a shareable minimal sample can help.

## Run locally

The site is static: there is no build step, backend or application dependency installation. Serve the repository using an existing static server, for example:

```sh
python -m http.server 8000 --bind 127.0.0.1
```

Open [http://127.0.0.1:8000](http://127.0.0.1:8000). Other static servers also work. Opening the HTML directly through the browser's local-file protocol can block bundled font loading.

## Maintenance and deployment

- `index.html`: UI, local import and comparison logic.
- `data/fontlab-data.js`: bundled font metadata, mapping ranges and Unicode blocks.
- `fonts/`: the 12 bundled font files.
- `licenses.html` and `licenses/`: font licensing and credits.
- `netlify.toml`: publishing and cache configuration.
- `README.md` and `README.en.md`: Chinese and English user guides.

The current setup uses **manual Netlify CLI deployments**, not Git-triggered deployment. A GitHub push alone does not update the live site.

After committing and pushing the intended changes, run this from the repository directory with the correct Netlify project already linked and authenticated:

```sh
netlify deploy --prod --dir=.
```

Review the publish directory first. Do not commit credentials or `.netlify/` state. Update the deployment instructions if automatic Git deployment is configured later.

The original generation and browser-check scripts live in the maintainer's local `07_字体盘点/_scripts/` directory and are **not included in this repository**. A clone is sufficient to serve the site, but does not include that regeneration pipeline. See the [Chinese maintenance notes](README.md#本地生成与验收脚本) for the original local commands.
