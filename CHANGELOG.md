# Changelog

All notable changes to this extension are documented in this file.

## [1.0.0] - Unreleased

### Added

- Syntax highlighting for AMO scripts and regen files of OpenScape 4000 / HiPath 4000 (`.amo`, `.amotext`, `.samtxt`)
- Commands in the form `ACTION-AMO:parameters;`, with the action word recognized in German (`EINRICHTEN`, `AENDERN`, `ABFRAGEN`, …), English (`ADD`, `CHANGE`, `DISPLAY`, …) or language-neutral (`EXEC`, `REGEN`, `START`, …), including short forms and lower case
- AMO names with digits (for example `DTIM1`)
- Regen files: the record prefix of `.samtxt` regens (`M2 1 `) is recognized
- Strings, hexadecimal values (`H'FFFF`), versions (`V10 R0.28.41`), decimals, `/* … */` comments (single line), message codes (`H500:`, `F04:`)
- Two colorblind-friendly themes (light and dark); German and English action words get different colors, English additionally in italics
- Snippets for common commands
- Bracket matching and auto-closing pairs

### Verified against

- OpenScape 4000 V11 AMO descriptions (German and English edition): AMO names and action words
- Real regen files (German, about 630,000 command lines): all command lines are recognized

### Known limits

- Comments that span several lines are not supported; a `/*` without `*/` ends at the end of the line
- Continuation lines of multi-line commands are not marked as commands
- Text like `FUNK-TEILNEHMER:` at the start of a line in output files looks like a command and is colored as one

## Feedback and issues

[GitHub Issues](https://github.com/bhuertgen/amo-language-vscode/issues)
