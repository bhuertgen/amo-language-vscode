# AMO Language Support for VSCode

Syntax highlighting and language support for **AMO** (the command language of OpenScape 4000 / HiPath 4000 PBX systems).

## Features

✨ **Syntax Highlighting** for:
- AMO Commands (CHANGE-FUNCT, AB-DATE, REG-FBTID, etc.)
- Parameters and arguments
- Comments (/* ... */)
- String literals ("...")
- Numeric values (decimal, hexadecimal, versions)
- Output headers (H500:, F04:, etc.)
- Status messages and responses

🎯 **Language Support**:
- German action words: `EINRICHTEN-…`, `AENDERN-…`, `ABFR-…`, `AB-…` and their short forms
- English action words: `ADD-…`, `CHANGE-…`, `DISPLAY-…` and their short forms
- Language-neutral action words: `EXEC-…`, `REGEN-…`, `START-…`
- Proper indentation rules
- Bracket/brace matching

📝 **Code Snippets** for common AMO commands:
- `chfunc` - Change language function
- `qdate` - Query date/time
- `qvegas` - Query VEGAS management
- `qaps` - Query program system
- `regapc` - Regenerate application config
- And more...

## File Types

The extension recognizes the following file types:
- `.amo` - AMO source files
- `.amotext` - AMO text output files
- `.samtxt` - Regen files (e.g. `Regen_xyz.txt.samtxt`)
- `.gpc` - Generation files of the PBX (same record format as regen files)
- `.pro` - Session logs of the AMO terminal (command echo `  <ABFRAGEN-…`, messages `L01:`, `M1:`, `H500:`). Note: `.pro` is also used by other tools (for example Qt project files); if VS Code picks the wrong language, choose it once via *Change Language Mode* or remove the association with `files.associations`.

In regen, `.gpc` and `.samtxt` files, the record prefix at the start of a line tells whether a command is active: `M1`/`M2` lines are commands, lines starting with `T1` or `T2` are commented out and are shown as comments (like `#` in other languages).

Plain `.txt` files are not associated automatically. To treat selected text files as AMO, add to your settings:

```json
"files.associations": { "**/regen/**/*.txt": "amo" }
```

## PDB Converter

This extension supports output from the **pdb2txt.exe** converter, which transforms binary PDB (Parameter Data Base) files from OpenScape 4000 systems into readable text format:

```bash
pdb2txt.exe {1|2} pdb-file
```

- `1` = German output
- `2` = English output

## Syntax Overview

### Commands
Commands follow the pattern `COMMAND-SUBCOMMAND:parameters;`

```amo
CHANGE-FUNCT:LANG=GER,SLANG=GER;
AB-DATE;
REG-FBTID;   /* REGENERIEREN-FBTID */
ABFR-PASSW;
```

### Parameters
Parameters are comma-separated, with optional ampersand concatenation:

```amo
ABFR-VEGAS:KURZ,ADS&SWU;
REGEN-APC;
```

### Data Types
- **Strings**: `"Text in quotes"`
- **Hex values**: `H'0000`, `H'FFFF`
- **Versions**: `V10 R0.28.41`, `V3.0`
- **Decimals**: `123`, `0001`

### Comments
Comments use C-style syntax and end at the end of the line:

```amo
/* Single-line comment */
```

Comments that span several lines are not supported.

### Output Messages
System output is marked with message codes (`H` = notice, `F` = error):

```amo
H500:  AMO DATE  GESTARTET
F04: ZIELDATEI EXISTIERT NICHT
```

## Installation

1. Open VSCode
2. Go to Extensions (Ctrl+Shift+X / Cmd+Shift+X)
3. Search for "AMO for OpenScape 4000"
4. Click Install
5. Reload VSCode

## Usage

Simply open any `.amo`, `.amotext` or `.samtxt` file. The extension automatically applies syntax highlighting.

## Examples

```amo
/* Switch the system language to German */
CHANGE-FUNCT:LANG=GER,SLANG=GER;

/* The same in a regen file (record prefix, German action) */
M2 1 AENDERN-FUNCT:LANG=GER;

/* Query with short forms */
AB-DATE;
abfr-passw;
```

## Supported AMO Commands

### Frequently Used Commands
- `CHANGE-FUNCT` / `AENDERN-FUNCT` - Change system functions
- `AB-DATE` / `ABFR-DATE` - Query date/time
- `AB-VEGAS` / `ABFR-VEGAS` - Manage generation workflows
- `AB-APS` / `ABFR-APS` - Query program systems
- `REGEN-ANUM` - Regenerate the system number (ANUM)
- `REGEN-APC` - Regenerate application management data (APC)
- `REGEN-DBC` - Regenerate database configuration (DBC)

And many more system commands...

## Language Variants

AMO commands consist of an action and an AMO name (`ACTION-AMO:parameters;`). The action exists in German and English, usually with short forms:

| German | English |
|--------|---------|
| `EINRICHTEN` (`EINR`) | `ADD` |
| `AENDERN` (`AEND`, `AE`) | `CHANGE` (`CHA`) |
| `LOESCHEN` (`LOES`, `LOE`) | `DELETE` (`DEL`) |
| `ABFRAGEN` (`ABFR`, `AB`) | `DISPLAY` (`DISP`, `DIS`) |
| `REGENERIEREN` | `REGENERATE` |
| `EINSCHALTEN` / `AUSSCHALTEN` | `ACTIVATE` (`ACT`) / `DEACTIVATE` (`DEACT`) |
| `KOPIEREN` | `COPY` |

Language-neutral actions: `EXECUTE`, `EXEC`, `EX`, `INIT`, `RES`, `REG`, `REGEN`, `START`, `STOP`, `RESTART`, `DUMP`, `INPUT`. Further English actions: `TEST`, `SET`, `OUTPUT`, `COLLECT`, `OPTIMIZE`, `RENAME`, `COPY`, `SWITCHOVER`; German: `SETZEN`, `KOPIEREN`, `PRUEFEN`, `UMSCHALTEN`, `SAMMELN`, `AUSGABE`, `UMBENENNEN`, `OPTIMIEREN`.

The AMO names (e.g. `SELG`, `DEFTM`, `DTIM1`) are identical in both languages. The action words were compiled from the OpenScape 4000 V11 AMO descriptions (German and English edition) and checked against real regen files. Abbreviations that occur in practice are included; other abbreviations may not be colored.

### Colors for German and English

The two bundled themes color the action word by language, so you can see at a glance which variant a script uses:

- German actions: orange, bold
- English actions: purple, bold and italic (a second cue besides color)
- Language-neutral actions: gray, bold

People with tritanopia (blue-yellow color blindness) can hardly tell the two languages apart by color; the italics are the distinguishing feature there.

## Contribution

To improve this extension, please:
1. Report issues
2. Submit feature requests
3. Share example files
4. Suggest additional keywords

## License

MIT License, see [LICENSE](LICENSE).

AMO, OpenScape, HiPath and Hicom are trademarks of their respective owners. This extension is an independent project and not affiliated with or endorsed by them.

## Related

- PDB Parameter Data Base format
