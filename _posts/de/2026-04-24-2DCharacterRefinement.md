---
title: Erweiterungen für meinen 2D Player Controller
date: 2026-04-24 02:00:00 +0200
lang: de
categories: [Studium, Module, 4FSC0PD002]
tags: [blog, github, github pages]
---

# Dokumentation: Erweiterter 2D Player Controller

## 1. Projektübersicht
* **Projektname:** [2DLearningSAE](https://git.zetacron.de/Yuuto/sae/src/branch/master/4FSC0PD002/2DLearningSAE)
* **Ziel:** Implementierung von Game-Feel Mechaniken zur Verbesserung des Platforming-Erlebnisses, inspiriert durch moderne Klassiker wie *Celeste*.

---

## 2. Implementierte Mechaniken

### Mechanik 1: Coyote Time
* **Inspiration:** *Super Mario*
* **Funktionsweise:** Dem Spieler wird ein kurzes Zeitfenster (z. B. 0.1s - 0.15s) gewährt, in dem er noch springen kann, nachdem er die Kante einer Plattform verlassen hat.
* **Einfluss auf das Spielgefühl:** Verhindert, dass der User bei knappen Sprüngen Frustriert wird.

### Mechanik 2: Jump Buffering
* **Inspiration:** *Hollow Knight*
* **Funktionsweise:** Wenn die Sprungtaste kurz vor der Landung gedrückt wird, speichert das System diesen Input und führt den Sprung automatisch im Moment des Bodenkontakts aus.
* **Einfluss auf das Spielgefühl:** Erlaubt flüssigere Kettenreaktionen und verhindert "verschluckte" Inputs bei schnellen Bewegungen.

### Mechanik 3: Wall Slide & Wall Jump
* **Inspiration:** *Celeste*
* **Funktionsweise:** Beim Kontakt mit einer Wand in der Luft wird die Fallgeschwindigkeit reduziert (Slide). Ein Sprunginput an der Wand löst einen Kraftstoß nach oben und in die entgegengesetzte Richtung aus.
* **Einfluss auf das Spielgefühl:** Erweitert das Leveldesign um vertikale Passagen und erhöht die Mobilität des Charakters.

---

## 3. Technische Umsetzung & Parameter
Die folgenden Parameter wurden im Inspector exponiert, um das Movement ohne Code-Änderungen feinjustieren zu können:

| Parameter | Beschreibung | Empfohlener Wert |
| :--- | :--- | :--- |
| `ParameterName` | Beschreibung | Wert |


---

## 4. Kombination der Mechaniken
Im Testlauf wurde besonders auf das Zusammenspiel geachtet:

---

## 5. Lösungswege

---

## 6. Bugs & Lösungen

---

## 7. Testprotokoll & Anpassungen
* **Test 1 (Coyote Time):** Wurde im bestehenden Level an einer weiten Schlucht getestet. Zeitwert wurde von 0.2s auf 0.12s gesenkt, da es sich sonst wie "Fliegen" anfühlte.
* **Test 2 (Wall Jump):** Überprüfung, ob der Spieler "unendlich" an einer Wand hochklettern kann. Entscheidung: [Ja/Nein, weil...].
* **Ergebnis:** Der Controller fühlt sich deutlich responsiver an und kleine Eingabefehler führen nicht mehr sofort zum Tod des Charakters.
