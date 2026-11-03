# Development

How to test, package and publish the extension locally. This file is not part of the package (`.vscodeignore`).

## Requirements

- VS Code 1.60 or later
- Node.js (for `npx`)
- No dependencies, there is no `npm install`

## Files

| File | Purpose |
|---|---|
| `package.json` | Manifest: name, version, file extensions (`.amo .amotext .samtxt .gpc .pro`), themes, snippets |
| `syntaxes/amo.tmLanguage.json` | TextMate grammar (scope `source.amo`) |
| `themes/*.json` | Two themes (light, dark) with colors for German, English and neutral actions |
| `snippets/amo.json` | Snippets |
| `language-configuration.json` | Brackets, comments, indentation |
| `testdaten/` | Made-up test files (no customer data), not packaged |

## Test in the Extension Development Host

1. Open the project folder in VS Code.
2. Press **F5**. A second window (Extension Development Host) opens with the extension loaded. The launch configuration lives in `.vscode/launch.json`, which is local and not in the repo; if it is missing, create one via *Run > Add Configuration > VS Code Extension Development* (`"type": "extensionHost"`, `--extensionDevelopmentPath=${workspaceFolder}`).
3. Open the files in `testdaten/`. Comments in the files describe the expected coloring.
4. Select a theme: **Ctrl+K Ctrl+T**, then *AMO Colorblind Friendly (Light)* or *(Dark)*.
5. After changing the grammar or themes: **Ctrl+Shift+P > Developer: Reload Window** in the second window.

To check which scope a token gets: **Ctrl+Shift+P > Developer: Inspect Editor Tokens and Scopes**.

## Package and install

```powershell
npx @vscode/vsce package --no-dependencies
code --install-extension amo-language-1.0.0.vsix
npx @vscode/vsce ls --no-dependencies
```

Uninstall: `code --uninstall-extension bhu.amo-language`.

`vsce ls` must list only the 10 package files: `CHANGELOG.md`, `LICENSE`, `README.md`, `package.json`, `language-configuration.json`, `images/icon.png`, `snippets/amo.json`, `syntaxes/amo.tmLanguage.json` and the two themes.

## Grammar rules

- Commands are matched at line start, optionally after the record prefix `M1 1 ` or the input mark `<`. This avoids false positives in running text.
- Command rules come before parameter keyword rules, otherwise e.g. `START` wins.
- Add new action words only with evidence (manual or real regen files) and maintain all three lists (German, English, neutral), upper and lower case.
- Backslashes in the JSON grammar are doubled (`\\s`); keep them intact when scripting edits.

## Never commit vendor or customer data

Vendor PDFs, PDB dumps, real regen files and customer data must never be committed. `doku/`, `Code-Beispiele/`, `memory/`, `*.pdf`, `*.pdb*`, `*.zip` and `*.samtxt` (except `testdaten/`) are gitignored. Before a release, check `git ls-files` and run `git grep -nE "[A-Z0-9]{60,}"` (must return nothing).

## Release and publishing

- Publisher: `bhu`, extension id `bhu.amo-language` (VS Code Marketplace and Open VSX namespace).
- Update `CHANGELOG.md` and the version in `package.json`.
- Publishing runs in GitHub Actions (`.github/workflows/publish.yml`) when a GitHub release is published; the release tag must match the `package.json` version (`v1.0.0` or `1.0.0`). Use *Run workflow* with `dry_run` to only build the VSIX.
- VS Code Marketplace uses Trusted Publishing (OIDC). A policy for publisher `bhu` must allow repo `amo-language-vscode`, workflow `publish.yml`, environment `publish`.
- Open VSX uses the `OVSX_PAT` secret in the `publish` environment; without it, Open VSX is skipped.