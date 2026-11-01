# h264toRGB

H.264-Videos auf dem **nativen RGB-Ausgang des Amiga** abspielen.
Der Raspberry Pi im PiStorm entschlüsselt das Video in Hardware
(VideoCore/MMAL), der Amiga zeigt es als **HAM6 in 192×144** mit bis zu
25 Bildern pro Sekunde.

*English summary below.*

Aktueller Stand: **V13Fix Quiet**. Diese Version hat **keinen Ton**.

## Was man braucht

- Amiga 500 oder 2000 mit **PiStorm** und **Raspberry Pi 3A+**
- **Emu68** (getestet mit 1.0.7)
- **AmigaOS 3.2.3** (`LoadMonDrvs` lädt alle Monitortreiber)
- 1 MB Chip-RAM reicht
- für 1080p-Filme in der `config.txt` des Pi: `gpu_mem=64`

Unter **CaffeineOS** gibt der RGB-Ausgang kein Bild, weil dort der
Native-Monitortreiber nicht geladen wird.

## Benutzung

Den Inhalt des Ordners [`amiga/`](amiga) in **eine** Schublade auf dem Amiga kopieren:

| Datei | Inhalt |
|---|---|
| `h264rgb` | das Programm |
| `ham.bin` | Tabelle für die HAM6-Umrechnung (16 MiB) |
| `edge.bin` | Tabelle (2 MiB) |
| `state.bin` | Tabelle (512 KiB) |

Die drei Tabellen müssen im **aktuellen Verzeichnis** liegen. Ohne sie läuft der Player nicht.

```
cd Work:h264toRGB
h264rgb film.mp4
```

- Filmformate: **MP4** und **MKV** mit H.264-Video
- **Ctrl-C** im selben Shell-Fenster beendet die Wiedergabe sauber.
  Danach kann man ohne Neustart den nächsten Film starten.
- `h264rgb ?` zeigt Version und Credits.

## Selbst bauen

Der Player ist komplett in 68000-Assembler geschrieben, die Quelle liegt in [`src/`](src).
Gebaut wird mit **vasm 2.0f** (`vasmm68k_mot`):

```
vasmm68k_mot -m68000 -Fhunkexe -nosym -o h264rgb h264rgb13fix.s
```

Auf dem Amiga geht das auch mit `Execute buildV13Fix`. Das Ergebnis ist genau
dieselbe Datei wie `amiga/h264rgb` (SHA-256 `30ce89ca…`, siehe `SHA256SUMS.txt`).

## Ältere Versionen

Unter [`history/`](history) liegen die Vorgänger mit Quelltext und Programmdatei:

| Version | Neu |
|---|---|
| V10P | MKV-Streaming, frühe Ablehnung nicht dekodierbarer Videos |
| V12H | MP4 und 1080p, zweiter Start ohne Neustart |
| V13 | Stopp mit Ctrl-C |
| V13Fix | wiederholtes Ctrl-C und einmaliges EOS korrigiert |
| V13Fix Quiet (aktuell) | wie V13Fix, aber ohne Diagnose-Ausgaben |

Die Tabellen `ham.bin`, `edge.bin` und `state.bin` sind in allen Versionen gleich.

## Credits

Gebaut von **MichiB210** zusammen mit ChatGPT.

## Lizenz

[MIT](LICENSE)

---

## English summary

h264toRGB plays H.264 video (MP4/MKV) on the Amiga's **native RGB output**.
The Raspberry Pi 3A+ in a PiStorm decodes the video in hardware via
VideoCore/MMAL. The Amiga shows it as **HAM6, 192×144, up to 25 fps**.
Current version: V13Fix Quiet, which has **no sound**.

Requirements: Amiga 500/2000 with PiStorm and Pi 3A+, Emu68 (tested 1.0.7),
AmigaOS 3.2.3, and `gpu_mem=64` for 1080p streams. On CaffeineOS the RGB
output shows no picture, because the Native monitor driver is not loaded.

Copy everything in `amiga/` into one drawer, `cd` there and run
`h264rgb movie.mp4`. The three `.bin` tables must be in the current
directory. Ctrl-C in the same shell stops playback cleanly.

To build, run `vasmm68k_mot -m68000 -Fhunkexe -nosym -o h264rgb src/h264rgb13fix.s`
with vasm 2.0f. The result is byte-identical to `amiga/h264rgb`.

License: MIT.
