# Mobile First vs. Desktop First — DevTools-Demos

Zwei kleine Demos, die zeigen was hinter Mobile First technisch steckt — direkt im Browser nachprüfbar. Nur HTML und CSS, keine Installation.

🔗 **[https://felinehuhn.github.io/mobile-first-demo/](https://felinehuhn.github.io/mobile-first-demo/)**

> Ausführliche Erklärung: **[research.md](./research.md)**

DevTools öffnen:
| | Shortcut |
|---|---|
| **macOS** | `Cmd + Option + I` |
| **Windows** | `F12` oder `Ctrl + Shift + I` |

Device-Toolbar (Handy simulieren):
| | Shortcut |
|---|---|
| **macOS** | `Cmd + Shift + M` |
| **Windows** | `Ctrl + Shift + M` |

---

## Demo 1 — Render-Blocking & Download-Priorität

**Frage:** Blockiert CSS, das für das aktuelle Gerät nicht gilt, trotzdem die Anzeige?
**Antwort:** Nein — es lädt mit *niedrigster* Priorität und blockiert den ersten Paint nicht.

1. Öffne `/demo1-render-blocking/`
2. DevTools → **Network** → Spalte **Priority** einblenden (Rechtsklick auf Spaltenüberschrift)
3. Device-Toolbar an (📱-Icon oben links in den DevTools) → **„iPhone SE"** → neu laden

| Datei | Priority | render-blocking? |
|---|---|---|
| `mobile.css` | **Highest** | ✅ ja |
| `desktop.css` (`media="(min-width:1024px)"`) | **Lowest** | ❌ nein |
| `print.css` (`media="print"`) | **Lowest** | ❌ nein |

**Gegenprobe:** Breite auf 1440 px stellen → neu laden → `desktop.css` springt auf **Highest**.

---

## Demo 2 — Responsive Images

**Frage:** Spart Mobile First Ladezeit?
**Antwort:** Ja — bei den Bildern, nicht beim CSS.

1. Öffne `/demo2-responsive-images/`
2. DevTools → **Network** → Filter auf **Img** → **„Disable cache"** anhaken
3. **„iPhone SE"** → neu laden → lädt `photo-480.png` ≈ 36 KB
4. **„Responsive / 1440 px"** → neu laden → lädt `photo-2000.png` ≈ 948 KB

~26× weniger Daten fürs Handy — allein durch `srcset`.
