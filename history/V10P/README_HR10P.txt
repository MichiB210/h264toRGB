H264RGB V10P - stabiler MKV/H264-Stand mit Kompatibilitaetscheck
=================================================================

Stand:
- H.264 ueber Raspberry Pi VideoCore/MMAL Hardwaredecoder.
- MKV/Matroska bounded-memory Streaming; keine Vollkopie der Filmdatei.
- 64-KiB MKV-Dateifenster, 256-KiB MMAL-Input fuer komplette Access Units bis 256 KiB.
- HAM6/RGB-Ausgabe: 192x144, maximal 25 fps.
- Mehrfachstart ohne Reboot ueber persistenten VCHIQ-Transport + frischen MMAL-Service/Decoder.
- Sauberes EOS-Cleanup mit COMPONENT_DISABLE + VCHIQ mmal CLOSE.
- Stream-DMA wird nach vollstaendigem EOS-Ownership-Gate freigegeben.

V10P:
- Die bekannte problematische SPS von 1.mkv wird VOR LUT-Laden, VCHIQ_INIT und MMAL erkannt.
- Fuer diesen hardwarebestaetigt nicht dekodierbaren Stream erscheint nur:
      Video wird nicht unterstuetzt.
- Der Decoder wird dabei nicht gestartet; dadurch ist danach kein Reboot erforderlich.
- Andere unterstuetzte MKV/H.264-Dateien laufen ueber den normalen Decoderpfad weiter.

Hardwarebestaetigt mit V10P:
- 2.mkv: vollstaendige Wiedergabe bis echtem EOS, Cleanup/DMA-Free OK.
- 3.mkv: direkt danach OHNE Reboot vollstaendige Wiedergabe bis echtem EOS, Cleanup/DMA-Free OK.
- 1.mkv: fruehe saubere Ablehnung als nicht unterstuetzt.

Benutzung:
    h264rgb film.mkv

Neuassemblieren auf dem Amiga:
    Execute buildhr10p

oder direkt:
    vasmm68k_mot -m68000 -Fhunkexe -nosym -o h264rgb h264rgb10p.s

Dateien:
- h264rgb          fertige Amiga-HUNK-EXE V10P
- h264rgb10p.s     kompletter 68000-Assembler-Quelltext
- buildhr10p       AmigaDOS-Buildskript
- README_HR10P.txt diese Datei
- SHA256SUMS.txt   SHA-256-Pruefsummen

Externe LUT-Dateien:
- ham.bin
- edge.bin
- state.bin

Diese drei Dateien sind gegenueber den vorherigen Versionen unveraendert und deshalb
nicht erneut im Paket enthalten. Sie muessen wie bisher neben h264rgb vorhanden sein.

Hinweis:
MP4-Containerunterstuetzung ist in V10P noch nicht enthalten; sie ist ein naechster
separater Entwicklungsschritt. Der stabile MKV-Pfad soll dabei erhalten bleiben.
