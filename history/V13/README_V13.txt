H264RGB V13 - CTRL-C STOP TEST
================================

Basis:
  Hardware-confirmed H264RGB V12H.

Only intended functional addition:
  Ctrl-C stops playback from the SAME shell/CLI process that started h264rgb.

No global ESC/raw-key hook is installed. Therefore normal Workbench programs on
HDMI keep their keyboard input.

Stop flow:
  Ctrl-C signal -> RGB display/Copper restored immediately
  -> no more source video is fed
  -> already submitted MMAL/VCHIQ work is drained
  -> normal decoder EOS is sent
  -> ownership gate must become safe
  -> existing V12H cleanup frees stream DMA and returns to the shell.

Important:
  V12H remains the frozen reference build.
  V13 is a hardware test until Ctrl-C has been confirmed on the real Amiga.

Test recommendation:
  1. Full reboot.
  2. Run: h264rgb erde.mp4
  3. Let the movie play for a while.
  4. Activate the SAME Shell window and press Ctrl-C.
  5. Save the complete log.
  6. Then, without reboot, start h264rgb erde.mp4 again to verify cleanup/restart.

Expected V13 stop lines:
  [V13] Ctrl-C erkannt: keine weiteren Videodaten; sende Decoder-EOS und raeume kontrolliert auf.
  [V13] STOPP PER CTRL-C: OK. ...

Tested 1080p configuration inherited from V12H:
  gpu_mem=64

Build:
  vasmm68k_mot -m68000 -Fhunkexe -nosym -o h264rgb h264rgb13.s
