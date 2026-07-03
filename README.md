# Mobile First vs. Desktop First — selbst in den DevTools nachprüfen

Zwei kleine Demos, mit denen du **selbst im Browser** siehst, worin der echte technische
Mobile-First-Vorteil liegt — und wo er ein Mythos ist. **Nur HTML und CSS, kein JavaScript**,
keine Installation, kein Build. (Perfekt, bevor ihr JS gelernt habt.)

> Die ausführliche Erklärung dazu steht in **[`text-fuer-studentin.md`](./text-fuer-studentin.md)**.
> Diese README ist die **Klick-für-Klick-Anleitung** für die DevTools.

---

## Starten (kein JavaScript, kein Server)

**Variante A — online (empfohlen, nichts installieren):**
👉 **[https://felinehuhn.github.io/mobile-first-demo/](https://felinehuhn.github.io/mobile-first-demo/)**
So verhalten sich **Download-Priorität, Caching und `srcset`** genau wie im echten Web.

**Variante B — lokal, nur angucken:**
Repo herunterladen (auf GitHub: *Code → Download ZIP*), entpacken und die Datei
**`index.html`** doppelklicken. Öffnet sich im Browser über `file://`.

> Hinweis: Über `file://` (Doppelklick) sieht man die Seiten und die Bild-Auswahl gut;
> die **Priority-Spalte** in Demo 1 zeigt sich am saubersten über die **online gehostete
> Version** (Variante A). Für den Unterricht also am besten den Pages-Link nutzen.

DevTools öffnen: **F12** (oder `Cmd/Ctrl + Alt/Option + I`).

---

## Demo 1 — Render-Blocking & Download-Priorität

**Frage:** Blockiert CSS, das für das aktuelle Gerät gar nicht gilt, trotzdem die Anzeige?
**Antwort im Network Tab:** Nein — es lädt mit *niedrigster* Priorität und blockiert den
ersten Paint nicht. Genau das nutzt Mobile First aus.

### Was du klickst
1. Öffne **`/demo1-render-blocking/`**.
2. DevTools → Tab **Network**.
3. Spalte **Priority** einblenden: Rechtsklick auf eine Spaltenüberschrift → **Priority** anhaken.
4. Handy simulieren: **Device-Toolbar** an (das 📱-Icon oben links in den DevTools,
   oder `Cmd/Ctrl + Shift + M`) → oben **„iPhone SE"** wählen.
5. Seite neu laden (`Cmd/Ctrl + R`).

### Worauf du achtest — erwartetes Ergebnis (Mobile)
| Datei | Priority | render-blocking? |
|---|---|---|
| `mobile.css` (kein `media`) | **Highest** | ✅ ja — das Fundament |
| `desktop.css` (`media="(min-width:1024px)"`) | **Lowest** | ❌ nein — lädt nachrangig |
| `print.css` (`media="print"`) | **Lowest** | ❌ nein — am Bildschirm nie |

### Gegenprobe
6. Device-Toolbar auf **„Responsive"** stellen, Breite auf **1440** ziehen, neu laden.
7. Jetzt springt **`desktop.css` auf Highest** — die Query trifft zu, also *jetzt* render-blocking.

➡️ **Erkenntnis:** Nicht die Schreibrichtung der Media Queries entscheidet, sondern *wer das
render-blockende Fundament bekommt*. Bei 70 % Mobile gehört das der Mobile-Basis.

*(Optional, für Fortgeschrittene: Tab **Performance** → Aufnahme mit Reload → die Markierung
**FCP / First Contentful Paint** liegt vor dem Abschluss von `desktop.css`.)*

---

## Demo 2 — Responsive Images

**Frage:** Spart Mobile First real Ladezeit? **Antwort:** Ja — aber bei den *Bildern*, nicht
beim CSS. Das Handy lädt eine kleine Bilddatei, der Desktop eine große — bei identischem HTML.

### Was du klickst
1. Öffne **`/demo2-responsive-images/`**.
2. DevTools → **Network** → Filter oben auf **Img**.
3. **„Disable cache"** anhaken (sonst wird beim zweiten Mal nichts neu geladen).
4. Device-Toolbar an → **„iPhone SE"** → neu laden.

### Worauf du achtest
- Es lädt **`photo-480.png` ≈ 36 KB**.
5. Jetzt auf **„Responsive / 1440px"** stellen → neu laden.
- Es lädt **`photo-2000.png` ≈ 948 KB**.

| Viewport | geladene Datei | Größe |
|---|---|---|
| iPhone SE | `photo-480.png` | **36 KB** |
| ~1440 px | `photo-2000.png` | **948 KB** |

➡️ **Erkenntnis:** ~26× weniger Daten fürs Handy — allein durch die richtige Bildauswahl.
So viel spart keine Media-Query-Schreibrichtung je ein. Zum Vergleich: eine ganze CSS-Datei
wiegt nur wenige KB. **Da** liegt die echte Ladezeit — und genau hier zahlt Mobile-First-Denken
(„Was braucht das kleine Gerät mindestens?") ein.

---

## Projektstruktur

```
mobile-first-demo-ohne-js/
├─ index.html                     Landing-Page mit Links zu beiden Demos
├─ text-fuer-studentin.md         die ausführliche Erklärung
├─ demo1-render-blocking/
│  ├─ index.html
│  ├─ mobile.css                  Basis, render-blocking
│  ├─ desktop.css                 media="(min-width:1024px)"
│  └─ print.css                   media="print"
└─ demo2-responsive-images/
   ├─ index.html
   └─ img/  photo-480.png · photo-1200.png · photo-2000.png
```

Alles nur **HTML, CSS und drei Bilddateien** — kein JavaScript, keine Tooling-Dateien.

## Kurzfazit

> Laden und Lesen sind bei Mobile und Desktop First gleich — einen Ladezeit-Vorteil hat
> Desktop First nicht. Nur beim **Rendern** kann eine Richtung gewinnen, zugunsten der
> **Mehrheit der Nutzer** (bei 70 % Mobile also Mobile First). Der große Ladezeit-Hebel
> sind ohnehin die **Bilder**, nicht das CSS.
