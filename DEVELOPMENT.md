# Entwicklung und Test

Diese Datei beschreibt, wie die Extension lokal getestet, gebaut und veröffentlicht wird. Sie gehört nicht zum Paket (`.vscodeignore`).

## Voraussetzungen

- VS Code ab 1.60
- Node.js (für `npx`)
- Keine Abhängigkeiten, es gibt kein `npm install`

## Dateien

| Datei | Zweck |
|---|---|
| `package.json` | Manifest: Name, Version, Dateiendungen (`.amo .amotext .samtxt .gpc .pro`), Themes, Snippets |
| `syntaxes/amo.tmLanguage.json` | TextMate-Grammatik (Scope `source.amo`) |
| `themes/*.json` | Zwei Themes (hell, dunkel) mit den Farben für Deutsch, Englisch und neutral |
| `snippets/amo.json` | Snippets |
| `language-configuration.json` | Klammern, Kommentar, Einrückung |
| `testdaten/` | Erfundene Testdateien (keine Kundendaten), nicht im Paket |

## Im Entwicklungsfenster testen

1. Projektordner in VS Code öffnen.
2. **F5** drücken (Konfiguration „AMO-Extension testen“ in `.vscode/launch.json`, lokal, nicht im Repo). Es öffnet sich ein zweites Fenster mit der Extension und dem Ordner `testdaten`.
3. Dateien in `testdaten` öffnen. Die Kommentare in den Dateien nennen die erwartete Färbung.
4. Eines der Themes wählen: **Strg+K, Strg+T**, dann *AMO Colorblind Friendly (Light)* oder *(Dark)*.
5. Nach Änderungen an Grammatik oder Themes im zweiten Fenster: **Strg+Umschalt+P → Developer: Reload Window**.

Fehlt `.vscode/launch.json`, mit *Ausführen → Konfiguration hinzufügen → VS Code Extension Development* neu anlegen. Wichtig sind `"type": "extensionHost"` und `--extensionDevelopmentPath=${workspaceFolder}`.

### Scope einer Farbe prüfen

Cursor auf das Wort setzen, dann **Strg+Umschalt+P → Developer: Inspect Editor Tokens and Scopes**. Der Scope zeigt, ob die Grammatik oder das Theme das Problem ist.

## Erwartete Färbung

| Zeile | Erwartung |
|---|---|
| `EINRICHTEN-ANSU:…;`, `AB-DATE;`, `L-WABE:5;` | deutsche Aktion: orange, fett (`keyword.control.action.de.amo`) |
| `ADD-ANSU:…;`, `CHANGE-FUNCT:…;`, `DISP-DATE;` | englische Aktion: violett, fett, kursiv (`keyword.control.action.en.amo`) |
| `EXEC-APC;`, `EX-`, `INIT-`, `RES-`, `REGEN-`, `START-` | neutrale Aktion: grau, fett (`keyword.control.action.amo`) |
| `chan-funct:…;`, `ab-date;` | wie oben, auch kleingeschrieben |
| `M1 1 …`, `M2 1 …` | aktive Befehlszeile; das Präfix ist gedimmt |
| Zeile beginnt mit `T1` oder `T2` | ganze Zeile Kommentar (auskommentierter Befehl) |
| `A001`, `B001`, `C001` | Kopfzeile, Kommentar |
| `-----…`, `Verbindung hergestellt zu: …` (`.pro`) | Kommentar |
| `  <ABFRAGEN-APS:…;` (`.pro`) | Eingabezeile, Befehl gefärbt |
| `L01:`, `M1:`, `H500:`, `F04:` am Zeilenanfang | Meldungscode, markiert |
| `AFR1`, `IPV4`, `DEV1` | keine Versionsfärbung |
| `1,2,3` | Zahlen getrennt, Kommas als Trenner |

## Als Paket testen

```powershell
npx @vscode/vsce package --no-dependencies
code --install-extension amo-language-1.0.0.vsix
```

Danach VS Code neu starten. Deinstallieren: `code --uninstall-extension bhuertgen.amo-language`.

Inhalt des Pakets prüfen:

```powershell
npx @vscode/vsce ls --no-dependencies
```

Erwartet sind 10 Dateien: `CHANGELOG.md`, `LICENSE`, `README.md`, `package.json`, `language-configuration.json`, `images/icon.png`, `snippets/amo.json`, `syntaxes/amo.tmLanguage.json` und die zwei Themes. Alles andere (`testdaten/`, `.vscode/`, Kunden- und Herstellerunterlagen) muss fehlen.

## Gegen echte Dateien testen (ohne Daten auszugeben)

Mit `vscode-textmate` und `vscode-oniguruma` (außerhalb des Repos installieren) lässt sich die Grammatik auf großen Regen prüfen. Gezählt werden nur Befehlszeilen und nicht erkannte Aktionswörter, nie der Inhalt. So wurde die Grammatik gegen 40 Regen (rund 620.000 Befehlszeilen), 45 `.gpc` und 40 `.pro` geprüft. Echte Kundendateien nie in das Repo legen.

## Regeln für die Grammatik

- Befehle stehen am Zeilenanfang, optional nach dem Satzpräfix `M1 1 ` oder der Eingabemarke `<`. Das verhindert Fehlalarme im Fließtext.
- Die Regeln für Befehle stehen vor den Regeln für Parameter-Schlüsselwörter, sonst gewinnt zum Beispiel `START`.
- Neue Aktionswörter nur mit Beleg aufnehmen (Handbuch oder echte Regen) und in allen drei Listen pflegen (deutsch, englisch, neutral), jeweils groß und klein.
- Die Backslashes in der JSON-Datei werden doppelt geschrieben (`\\s`). Beim Skripten aufpassen, dass sie nicht verloren gehen.

## Vor einem Release

1. `CHANGELOG.md` und Version in `package.json` anpassen.
2. Testdateien in `testdaten` durchsehen (Färbung wie in der Tabelle).
3. Paket bauen und installieren, Test wie oben.
4. Prüfen, dass keine Kundendaten im Repo stehen: `git grep -nE "[A-Z0-9]{60,}"` darf keinen Treffer liefern (lange Codewörter), und die Dateiliste von `git ls-files` enthält nur die gewünschten Dateien.
5. Veröffentlichen (erst nach Freigabe):
   ```powershell
   npx @vscode/vsce publish --no-dependencies    # VS Code Marketplace, Publisher bhuertgen, Token nötig
   npx ovsx publish -p <token>                   # Open VSX, Namespace bhuertgen
   ```
   Für beide Stores muss der Publisher beziehungsweise Namespace `bhuertgen` vorher angelegt sein.
