# Codex-Anleitung fuer das Orga-Team

Diese Anleitung ist fuer Mitglieder des Orga-Teams, die mit Codex am Repo arbeiten sollen:

https://github.com/VibecodingGermany/mangoblau-sommerfest-2026

Ziel: Aufgaben pflegen, Issues aktualisieren und Project-Daten sauber halten, ohne das Board oder die Issues zu zerstoeren.

## Grundregel

Bitte keine Massenscripts ausfuehren, wenn ihr nicht genau wisst, was sie tun.

Normale Orga-Arbeit passiert im Browser:

- Issue kommentieren
- Status im Project aendern
- Assignee setzen
- Due Date setzen
- Sub-Issue abhaken oder kommentieren

Codex/Scripts nur fuer koordinierte Massenupdates nutzen.

## Was nicht ausgefuehrt werden darf

Diese Scripts duerfen nicht erneut ausgefuehrt werden, ausser Elvira oder die technische Orga sagt es ausdruecklich:

```powershell
.\import-github-event-plan.ps1
.\convert-checklists-to-subissues.ps1
```

Warum:

- `import-github-event-plan.ps1` erstellt Issues neu. Nochmaliges Ausfuehren erzeugt Duplikate.
- `convert-checklists-to-subissues.ps1` erstellt Sub-Issues. Nochmaliges Ausfuehren kann doppelte Teilaufgaben erzeugen, falls die Schutzlogik nicht alle Sonderfaelle erkennt.

## Was meistens sicher ist

Diese Scripts sind fuer gezielte Nachpflege gedacht, aber trotzdem nur nach Absprache ausfuehren:

```powershell
.\fix-github-umlauts.ps1
.\set-all-project-due-dates.ps1
.\link-existing-issues-to-project.ps1
.\update-project-from-whatsapp.ps1
.\upload-readme.ps1
```

Vor jedem Lauf pruefen:

- Bin ich im richtigen Ordner?
- Ist das richtige Repo gemeint?
- Habe ich den richtigen Token gesetzt?
- Wurde das Script heute schon von jemand anderem ausgefuehrt?
- Erzeugt das Script neue Issues oder aktualisiert es nur vorhandene?

## Rechte, die ihr braucht

Ihr braucht Zugriff auf:

- Organisation: `VibecodingGermany`
- Repo: `VibecodingGermany/mangoblau-sommerfest-2026`
- Project: `VibecodingGermany Project 2`

Empfohlene Rechte:

- Repo: `Triage` fuer normale Issue-Arbeit
- Repo: `Write` fuer Scripts oder Datei-Updates
- Project: `Write` fuer Status, Due Date und Boardpflege

## Token erstellen

Jede Person nutzt ihren eigenen Token. Niemals Tokens teilen.

1. GitHub oeffnen:

   https://github.com/settings/tokens

2. Links waehlen:

   `Personal access tokens` -> `Tokens (classic)`

3. Klicken:

   `Generate new token` -> `Generate new token (classic)`

4. Werte setzen:

   - Note: `sommerfest-codex`
   - Expiration: `7 days` oder `30 days`

5. Scopes setzen:

   ```text
   repo
   project
   ```

6. Token erstellen und direkt kopieren.

7. Falls GitHub SSO fuer `VibecodingGermany` verlangt:

   - nach dem Erstellen beim Token auf `Configure SSO`
   - Organisation `VibecodingGermany` autorisieren

## Token in PowerShell setzen

Im Codex-/PowerShell-Arbeitsordner:

```powershell
cd "C:\Users\elvir\Documents\Codex\2026-05-14\kannst-du-darauf-zugreifen-https-github"
```

Token setzen:

```powershell
$env:GITHUB_TOKEN="DEIN_TOKEN_HIER"
```

Pruefen, ob ein Token gesetzt ist:

```powershell
if ($env:GITHUB_TOKEN) { "Token ist gesetzt" } else { "Token fehlt" }
```

Token nicht in Screenshots, Chats oder Issues posten.

## Token nach der Arbeit entfernen

In der aktuellen PowerShell-Sitzung:

```powershell
Remove-Item Env:\GITHUB_TOKEN
```

Optional in GitHub loeschen:

https://github.com/settings/tokens

## Sicherer Codex-Prompt fuer normale Mitarbeit

Diesen Text koennen Mitglieder in Codex einfuegen, wenn sie vom Repo aus arbeiten:

```text
Du arbeitest im Repo VibecodingGermany/mangoblau-sommerfest-2026 fuer das Mangoblau Sommerfest 2026.

Bitte zuerst README.md und die vorhandenen Issues/Project-Struktur beachten.

Wichtig:
- Keine neuen Massenimports starten.
- import-github-event-plan.ps1 nicht ausfuehren.
- convert-checklists-to-subissues.ps1 nicht ausfuehren.
- Keine Issues loeschen.
- Keine Labels, Milestones oder Project-Felder ohne Rueckfrage umbenennen.
- Keine erledigten Aufgaben wieder oeffnen, ausser ich sage es ausdruecklich.
- Keine Tokens, Telefonnummern oder privaten Chatdetails in Issues schreiben.

Wenn ich dir Chatnotizen gebe:
- Fasse sie sachlich zusammen.
- Ordne sie passenden bestehenden Issues zu.
- Aktualisiere nur relevante Issues.
- Schreibe Entscheidungen als Kommentar oder in den Abschnitt "Gruppenstand aus WhatsApp".
- Setze Status und Due Date nur, wenn es logisch eindeutig ist.
- Wenn unklar ist, erst nachfragen.

Bevor du Scripts oder Massenupdates ausfuehrst:
- Erklaere genau, welche Issues betroffen sind.
- Erklaere, ob neue Issues entstehen.
- Warte auf meine Bestaetigung.
```

## Sicherer Codex-Prompt fuer ein einzelnes Issue

```text
Bitte aktualisiere nur Issue #NUMMER im Repo VibecodingGermany/mangoblau-sommerfest-2026.

Aufgabe:
[hier beschreiben]

Regeln:
- Keine anderen Issues aendern.
- Kein Massenupdate.
- Keine neuen Issues erstellen.
- Bestehende Beschreibung erhalten und nur sinnvoll ergaenzen.
- Wenn du unsicher bist, erst fragen.
```

## Sicherer Codex-Prompt fuer Gruppenchat-Zusammenfassung

```text
Bitte fasse diese Chatnotizen fuer das Orga-Repo zusammen.

Repo: VibecodingGermany/mangoblau-sommerfest-2026
Project: https://github.com/orgs/VibecodingGermany/projects/2/views/1

Regeln:
- Keine Telefonnummern uebernehmen.
- Keine privaten Details uebernehmen, ausser sie sind fuer die Orga noetig.
- Keine Duplikat-Issues erstellen.
- Bestehende Issues verwenden.
- Pro betroffenem Issue einen kurzen sachlichen Gruppenstand ergaenzen.
- Wenn Status oder Due Date unklar sind, nicht raten, sondern Rueckfrage stellen.

Chatnotizen:
[hier einfuegen]
```

## Welche Scripts was tun

### `link-existing-issues-to-project.ps1`

Fuegt vorhandene Issues ins Project ein und setzt Status/Due Date.

Nur nutzen, wenn Issues im Repo existieren, aber im Project fehlen.

### `set-all-project-due-dates.ps1`

Setzt sinnvolle Due Dates relativ zum Event am 27.06.2026.

Standardverhalten:

- erledigte Karten mit vorhandenem Due Date bleiben unveraendert
- offene/in Arbeit befindliche Karten bekommen die Planungsfrist

### `fix-github-umlauts.ps1`

Repariert kaputte Zeichen wie:

```text
fÃ¼r -> fuer/für
Ã¤ -> ä
Ã¶ -> ö
Ã¼ -> ü
```

Nur nutzen, wenn wirklich Encoding-Fehler sichtbar sind.

### `update-project-from-whatsapp.ps1`

Traegt vorbereitete Gruppenstaende aus WhatsApp in bestimmte Issues ein.

Nur nutzen, wenn die enthaltenen Updates noch nicht eingetragen wurden.

### `upload-readme.ps1`

Laedt die lokale `README.md` ins Repo hoch.

Nur nutzen, wenn die README im Repo aktualisiert werden soll.

### `import-github-event-plan.ps1`

Erstellt Labels, Milestones und Issues.

Nicht erneut ausfuehren.

### `convert-checklists-to-subissues.ps1`

Erstellt aus Checklist-Punkten echte GitHub Sub-Issues.

Nicht erneut ausfuehren, ausser bewusst fuer neue, noch nicht konvertierte Parent-Issues.

## Vor einem Scriptlauf

Diese Checkliste abarbeiten:

- [ ] Ich bin im richtigen Ordner.
- [ ] Ich habe meinen eigenen Token gesetzt.
- [ ] Ich weiss, welches Repo betroffen ist.
- [ ] Ich weiss, welches Project betroffen ist.
- [ ] Ich weiss, ob neue Issues erstellt werden.
- [ ] Ich habe im Team gefragt, ob das Script schon gelaufen ist.
- [ ] Ich habe verstanden, wie ich das Ergebnis pruefe.

## Nach einem Scriptlauf

Im GitHub Project pruefen:

- Sind neue Karten entstanden?
- Gibt es Duplikate?
- Stimmen Status-Spalten?
- Stimmen Due Dates?
- Wurden erledigte Aufgaben nicht versehentlich wieder geoeffnet?
- Sind keine privaten Daten im Issue gelandet?

## Wenn etwas schiefgeht

Nicht weiter herumprobieren.

Stattdessen:

1. Scriptausgabe kopieren.
2. Sagen, welches Script ausgefuehrt wurde.
3. Sagen, wann es ausgefuehrt wurde.
4. Link zu einem betroffenen Issue schicken.
5. Keine weiteren Massenupdates starten, bis klar ist, was passiert ist.

## Kurzfassung

- Normale Arbeit im Browser machen.
- Tokens nicht teilen.
- Massenscripts nur nach Absprache.
- Keine Import-Scripts nochmal ausfuehren.
- Keine privaten Chatdaten in Issues kopieren.
- Bei Unsicherheit erst fragen.
