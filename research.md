# Mobile First vs. Desktop First — was technisch stimmt

Die Frage, ob Desktop First beim Laden und Rendern schneller ist, lässt sich klar beantworten — wenn man drei Begriffe trennt, die oft in einen Topf geworfen werden.

---

## 1. Drei getrennte Schritte: Laden ≠ Lesen ≠ Rendern

```
LADEN  ─────►  LESEN  ─────►  RENDERN
(Netzwerk)    (CPU/CSSOM)     (Layout + Paint = das, was man sieht)
```

**Laden = Download / Netzwerk**
Der Browser holt die Bytes über die Leitung (`style.css`, Bilder, JS …).
- Kostet: Netzwerkzeit. Hängt ab von Dateigröße, Verbindung, Download-**Priorität**.
- Im **Network Tab** sichtbar (Spalten *Size* / *Time*, Wasserfall).

**Lesen = Parsen / CSSOM bauen**
Der Browser *versteht* die geladenen Bytes: Er zerlegt jede Regel und baut daraus die
CSSOM (das interne Regel-Modell).
- Kostet: CPU-Zeit. Er parst **alle** Regeln — auch die in `@media`-Blöcken, die gerade
  nicht zutreffen. Nur *angewendet* werden sie nicht.
- Im **Performance-Panel** sichtbar als *„Parse Stylesheet" / „Recalculate Style"*.

**Rendern = Layout + Paint**
Aus DOM + CSSOM baut der Browser den Render-Tree, rechnet das Layout und
malt es auf den Bildschirm. Das ist der Moment „die Seite ist da" (First Contentful Paint).
- Kostet: CPU/GPU-Zeit.
- Im **Performance-Panel** sichtbar als *„Layout", „Paint"* und die *FCP*-Markierung.

**Der Zusammenhang „render-blocking":** CSS ist standardmäßig render-blocking — Schritt 3
(Rendern) startet erst, wenn Laden **und** Lesen des render-blockenden CSS fertig sind.

---

## 2. Ist Desktop First schneller? — Nein.

Es gibt zwei reale Fälle:

### Fall 1 — alles in EINER gebündelten CSS-Datei (der Normalfall)

| Schritt | Mobile First (`min-width`) vs. Desktop First (`max-width`) |
|---|---|
| **Laden** | identisch — die komplette Datei wird immer geladen |
| **Lesen** | identisch — alle Regeln werden geparst |
| **Rendern** | identisch — gleiche Regeln, gleiches Ergebnis |

→ Bei einer gebündelten Datei ist es **performance-neutral**. Desktop First ist hier
**nicht schneller** — es ist gleich schnell.

### Fall 2 — CSS in mehrere Dateien mit `media`-Attribut gesplittet

Hier gibt es einen echten Render-Vorteil — aber er zeigt in die **andere** Richtung.
Ein Stylesheet, dessen `media`-Query gerade nicht zutrifft, wird zwar noch *geladen*, aber
mit **niedrigster Priorität** und **ohne den ersten Paint zu blockieren**.

Entscheidend ist dann: *Wer bekommt das kleine, render-blockende Fundament?* Antwort: **die
Mehrheit der Nutzer.** Genau hier wird die 70-%-Zahl vom Business- zum **technischen**
Argument: Bei 70 % Mobile gehört die Mobile-Basis auf den kritischen Pfad (schnellster Paint
für die Mehrheit), die Desktop-Ergänzung wird nachrangig geladen.

**Wann ist Desktop First die bessere Wahl?** Wenn die Nutzer mehrheitlich am Desktop sitzen
(B2B-Tools, Dashboards, interne Admin-Oberflächen). Dann gehört die Desktop-Basis auf den
kritischen Pfad. Merksatz: **Das render-blockende Fundament gehört der Mehrheit der Nutzer.**

---

## 3. Wenn beides gleich schnell ist — warum dann trotzdem Mobile First?

Im Normalfall (eine gebündelte Datei) sind Laden/Lesen/Rendern identisch. Dann entscheiden
**keine** Performance-Gründe, sondern diese:

1. **Die Mehrheit bekommt die durchdachte Erfahrung.** ~70 % Mobile-Traffic heißt: Mobile
   First gestaltet die Erfahrung für die meisten Nutzer *primär* — nicht als nachträglich
   gequetschte Notlösung.

2. **Die intuitivere Richtung: klein → groß statt groß → klein.** Es ist einfacher,
   von einer schlichten Basis auszugehen und für mehr Platz *Dinge hinzuzufügen*
   (Progressive Enhancement), als ein üppiges Desktop-Layout für kleine Screens wieder
   *zurückzubauen*. Beim Verkleinern übersieht man leicht Kanten (Overflow, zu große
   Abstände, Hover-Only-Funktionen ohne Touch-Alternative).

3. **Weniger Code? — teilweise, aber ehrlich betrachtet:**
   Der **Gesamtumfang** ist bei beiden Ansätzen ungefähr gleich. Aber Desktop First
   (`max-width`, subtraktiv) braucht tendenziell mehr **reine Override-/Reset-Zeilen**,
   deren einziger Zweck es ist, einen Desktop-Wert wieder aufzuheben:
   ```css
   /* Desktop First: erst aufbauen, dann zurücksetzen */
   .grid { display: grid; grid-template-columns: repeat(3,1fr); padding: 40px; }
   @media (max-width: 767px) {
     .grid { display: block; padding: 10px; }
   }

   /* Mobile First: additiv, nichts zurückzunehmen */
   .grid { display: block; padding: 10px; }
   @media (min-width: 768px) {
     .grid { display: grid; grid-template-columns: repeat(3,1fr); padding: 40px; }
   }
   ```
   → Mobile First ist oft **etwas schlanker und sauberer** in der Kaskade.
   Der Gewinn ist aber **Lesbarkeit/Wartbarkeit**, *nicht* Geschwindigkeit — die paar Bytes
   Unterschied sind für die Ladezeit irrelevant.

4. **Priorisierung wird erzwungen.** Wenig Platz zwingt zur Frage „Was ist hier wirklich
   wichtig?" — das verhindert überladene Layouts von vornherein.

5. **Der einzige *große* Ladezeit-Hebel liegt sowieso nicht im CSS.** Bilder, Fonts und JS
   wiegen ein Vielfaches der CSS-Datei. Über `srcset`/`sizes` lädt ein Handy real ein
   kleines Bild (z. B. 36 KB) statt der Desktop-Variante (z. B. 948 KB). *Diese* Ersparnis
   ist echte Ladezeit — und sie entspringt dem Mobile-First-Denken, nicht der Schreibrichtung
   der Media Queries.

---

## Fazit

> Laden und Lesen sind bei Mobile und Desktop First gleich — einen Ladezeit-Vorteil hat
> Desktop First nicht. Nur beim **Rendern** kann eine Richtung gewinnen, und zwar zugunsten
> der **Mehrheit der Nutzer** (bei 70 % Mobile also Mobile First). Ansonsten spricht für
> Mobile First die intuitivere Richtung (klein → groß), die sauberere Kaskade und die echte
> Byte-Ersparnis bei Bildern — nicht die CSS-Datei selbst.

---

## Quellen

- [Render-blocking CSS — web.dev](https://web.dev/critical-rendering-path-render-blocking-css/)
- [Using media queries — MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_media_queries/Using_media_queries)
- [Using responsive images in HTML (`srcset`/`sizes`) — MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Responsive_images)
- [Loading CSS without blocking render — Keith Clark](https://keithclark.co.uk/articles/loading-css-without-blocking-render/)
