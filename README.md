# sound-fix-ubuntu24
## 1. check if your audio device is detected
Run the following command:
```bash
pactl list short sinks
```
If your audio devices are detected correctly, you should see entries similar to:
```bash
408	alsa_output.pci-0000_01_00.1.hdmi-stereo	PipeWire	s32le 2ch 48000Hz	IDLE
439	alsa_output.pci-0000_00_1f.3.analog-stereo	PipeWire	s32le 2ch 48000Hz	RUNNING
```
However, if you see something like this:
```bash
33 auto_null PipeWire float32le 2ch 48000Hz SUSPENDED
``` 
it means your audio device was not detected properly, and PipeWire is falling back to a dummy (null) output instead of a real audio device.
