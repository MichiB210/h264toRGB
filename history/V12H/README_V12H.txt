H264RGB V12H - FULL PACKAGE
===========================

Basis: V12G.

V12G:
- first 1080p MP4 run remained successful
- restart no longer crashed
- restart timed out on the first ISP PORT_INFO_GET

V12H:
- first-run path remains unchanged
- second-run ISP PORT_INFO_GET calls are routed through the already proven
  local-port-1 v9f PORT_INFO_GET worker used successfully during restart
  decoder configuration
- other V12G restart changes remain in place

Test:
1. Full reboot.
2. h264rgb erde.mp4 until clean EOS.
3. Without reboot run h264rgb erde.mp4 again.
4. Save complete log.

Expected second-run line:
[V12H] Zweitstart: PORT_INFO_GET laeuft ueber bewaehrten local-1/v9f-Worker.

gpu_mem=64 remains required for the tested 1080p stream.
