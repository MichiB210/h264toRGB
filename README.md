# h264toRGB

H.264-Videos auf dem **nativen RGB-Ausgang des Amiga** abspielen, **mit Ton über Paula**.
Der Raspberry Pi im PiStorm entschlüsselt das Video in Hardware
(VideoCore/MMAL), der Amiga zeigt es als **HAM6 in 192×144** mit bis zu
25 Bildern pro Sekunde.

*English summary below.*

Aktueller Stand: **V14D**.

## Was man braucht

- Amiga 500 oder 2000 mit **PiStorm** und **Raspberry Pi 3A+**
- **Emu68 1.0.7** (unter Emu68 1.1 Beta läuft h264rgb derzeit nicht)
- **AmigaOS 3.2.3** (`LoadMonDrvs` lädt alle Monitortreiber)
- 1 MB Chip-RAM reicht
- für 1080p-Filme in der `config.txt` des Pi: `gpu_mem=64`
- ein **PAL-Amiga**: h264rgb stellt den RGB-Ausgang während des Films fest auf PAL 50 Hz

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
h264rgb film.mkv
```

- Filmformate: **MKV** und **MP4** mit H.264-Video (siehe unten, was nicht geht)
- **Ctrl-C** im selben Shell-Fenster beendet die Wiedergabe sauber.
  Danach kann man ohne Neustart den nächsten Film starten.
- `h264rgb ?` zeigt Version und Credits.

## Ton

Der Ton wird **vorher am PC** (oder mit ffmpeg auf dem Amiga) in eine einfache
Datei umgewandelt, die neben dem Film liegt und genauso heißt, nur mit `.snd`:

```
ffmpeg -i film.mkv -vn -ac 2 -ar 22050 -f s8 film.snd
```

- Format: 8 Bit signed, Stereo, 22050 Hz, ohne Header (etwa 2,6 MB pro Minute).
- So geht jede Tonspur, die ffmpeg lesen kann: AAC, MP3, AC3, E-AC3, **DTS**, Opus,
  FLAC, auch 5.1 (`-ac 2` mischt auf Stereo herunter).
- Die Datei wird vor dem Start ganz ins Fast-RAM geladen.
- Paula spielt links über Kanal 0 und rechts über Kanal 1, aus einem Ringpuffer im
  Chip-RAM. Bild und Ton laufen am selben Takt (VBlank und Paula hängen am selben
  Quarz) und können nicht auseinanderlaufen.
- Ohne `.snd`-Datei läuft der Film stumm.

## Statistik

Am Ende zeigt h264rgb drei Zeilen, zum Beispiel:

```
Statistik: dekodiert 3958, gezeigt 3958, ausgelassen 0, verspaetet 0
Laufzeit VBlanks 8253, Ton-Samples 3640320
Paula-Runden 223, Korrekturen 0, Bildwechsel 49.92 Hz
```

**Bildwechsel** sollte bei etwa 49,92 Hz liegen. **Korrekturen** sollte 0 sein.

## Was nicht unterstützt wird

- **Andere Videoformate als H.264**: kein H.265/HEVC, VP9, AV1, MPEG-2, DivX/Xvid.
- **Andere Container**: nur `.mkv` und `.mp4` (erkannt an der Endung), kein AVI, TS, MOV, M4V.
- **10-Bit-Video** (High 10) und 4:2:2 – das kann der Hardware-Decoder des Pi 3 nicht.
- **Größer als 1080p** (zum Beispiel 4K).
- **Filme ohne Bildrate im Video-Kopf** (SPS ohne VUI `timing_info`). h264rgb meldet dann
  „FEHLER FPS“.
- **Über 50 Bilder pro Sekunde**. Filme über 25 Bilder pro Sekunde werden auf 25 reduziert.
- **Einzelbilder größer als 256 KiB** (sehr hohe Bitraten).
- **MP4 mit sehr großem Inhaltsverzeichnis** (`moov` über 8 MiB).
- Ton direkt aus dem Film: Der Ton muss vorher als `.snd` umgewandelt werden.

## Selbst bauen

Der Player ist komplett in 68000-Assembler geschrieben, die Quelle liegt in [`src/`](src).
Gebaut wird mit **vasm 2.0f** (`vasmm68k_mot`):

```
vasmm68k_mot -m68000 -Fhunkexe -nosym -o h264rgb h264rgb14d.s
```

Auf dem Amiga geht das auch mit `Execute buildV14D`. Das Ergebnis ist genau
dieselbe Datei wie `amiga/h264rgb` (siehe `SHA256SUMS.txt`).

## Ältere Versionen

Unter [`history/`](history) liegen die Vorgänger mit Quelltext und Programmdatei:

| Version | Neu |
|---|---|
| V10P | MKV-Streaming, frühe Ablehnung nicht dekodierbarer Videos |
| V12H | MP4 und 1080p, zweiter Start ohne Neustart |
| V13 | Stopp mit Ctrl-C |
| V13Fix | wiederholtes Ctrl-C und einmaliges EOS korrigiert |
| V13Fix Quiet | ohne Diagnose-Ausgaben (letzte Version ohne Ton) |
| V14A | Paula-Ton aus einer `.snd`-Datei (Test) |
| V14B | Bild bleibt beim Ton, Statistik am Ende (Test) |
| V14C | Ton aus einem Ringpuffer statt Pufferwechsel (Test) |
| **V14D** (aktuell) | RGB fest auf PAL 50 Hz, gemessene Bildwechsel-Frequenz |

Die Tabellen `ham.bin`, `edge.bin` und `state.bin` sind in allen Versionen gleich.

## Credits

Gebaut von **MichiB210** zusammen mit ChatGPT und Claude.

## Lizenz

[MIT](LICENSE)

---

## English summary

h264toRGB plays H.264 video (MKV/MP4) on the Amiga's **native RGB output**, with
**sound through Paula**. The Raspberry Pi 3A+ in a PiStorm decodes the video in
hardware via VideoCore/MMAL. The Amiga shows it as **HAM6, 192×144, up to 25 fps**.
Current version: V14D.

Requirements: Amiga 500/2000 (PAL) with PiStorm and Pi 3A+, Emu68 1.0.7,
AmigaOS 3.2.3, and `gpu_mem=64` for 1080p streams. h264rgb forces the chipset
to PAL 50 Hz while the film plays.

Copy everything in `amiga/` into one drawer, `cd` there and run
`h264rgb movie.mkv`. The three `.bin` tables must be in the current directory.
Ctrl-C in the same shell stops playback cleanly.

Sound: convert the soundtrack first, e.g.
`ffmpeg -i movie.mkv -vn -ac 2 -ar 22050 -f s8 movie.snd`, and put `movie.snd`
next to the film. Any format ffmpeg can decode works (AAC, AC3, DTS, ...).
Paula plays it from a looping Chip-RAM ring locked to the same clock as the
picture. Without a `.snd` file the film plays silently.

Not supported: codecs other than H.264, containers other than `.mkv`/`.mp4`,
10-bit or 4:2:2 video, above 1080p, streams whose SPS has no VUI timing info,
more than 50 fps, single frames over 256 KiB, MP4 `moov` over 8 MiB.

To build, run `vasmm68k_mot -m68000 -Fhunkexe -nosym -o h264rgb src/h264rgb14d.s`
with vasm 2.0f. The result is byte-identical to `amiga/h264rgb`.

License: MIT.
