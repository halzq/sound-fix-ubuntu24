# sound-fix-ubuntu24
## 1. Check if your audio device is detected
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


## 2. Edit ALSA’s modprobe configuration file
### 2.1 Edit ALSA Configuration
Run the following command to open ALSA’s configuration file:
```bash
sudo nano /etc/modprobe.d/alsa-base.conf
```
This file is used to pass options to kernel sound modules (drivers) when they are loaded.
Add the following line to the file:
```bash
options snd-hda-intel model=auto
```
* On a desktop, model=auto is usually the best choice.
* On a laptop, you may need a specific model. Refer to the [link](https://www.kernel.org/doc/html/latest/sound/hd-audio/models.html).

Common values include:
* auto → Let ALSA guess (default, usually best for modern kernels).
* thinkpad → For ThinkPad laptops.
* dell-headset-multi → For Dell laptops with combo jack.
* asus → For ASUS laptops.
* hp → For HP laptops.

### 2.2 Apply the changes
Reload ALSA:
```bash
sudo alsa force-reload
```
Restart PipeWire and WirePlumber:
```bash
systemctl --user restart pipewire pipewire-pulse wireplumber
```

### 2.3 Verify audio devices
Check available sinks:
```bash
pactl list short sinks
```
You should see real devices, for example:
```
alsa_output.pci-0000_00_1f.3.analog-stereo
alsa_output.pci-0000_01_00.1.hdmi-stereo
```
however, if you are still not seeing your audio device or only see auto_null,
then your audio device was not detected correctly, and the issue may be with the driver rather than the configuration.


## 3. Fix NVIDIA Drive (if using NVIDIA GPU)
Sometimes HDMI audio won’t show up until the NVIDIA driver is installed correctly.

### 3.1 Check driver status
```bash
nvidia-smi
```
If you see:
```bash
NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
```
it means the driver is missing or not working.

### 3.2 Find recommended drivers
```bash
ubuntu-drivers devices
```

You’ll see something like:
```
driver : nvidia-driver-535 - distro non-free recommended
driver : nvidia-driver-560 - distro non-free
....
```

### 3.3 Install the recommended driver
Replace nvidia-driver-535 with the version shown on your system:
```bash
sudo apt update
# sudo apt install nvidia-driver-535 -y
sudo apt install your-driver-version -y
sudo reboot
```


### 3.4 Verify installation
After reboot:
``` bash
nvidia-smi
```
You should see driver version and GPU details like this: 
```bash
Wed Aug 27 00:37:00 2025       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.247.01             Driver Version: 535.247.01   CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
```

### 3.5 Check sinks again
```bash
pactl list short sinks
```
If your audio devices are detected correctly, you should see entries similar to:
```bash
408	alsa_output.pci-0000_01_00.1.hdmi-stereo	PipeWire	s32le 2ch 48000Hz	IDLE
439	alsa_output.pci-0000_00_1f.3.analog-stereo	PipeWire	s32le 2ch 48000Hz	RUNNING
```

“The steps above are how I fixed my HDMI audio output problem. I am using the built-in speakers of my monitor.”
“If this guide helped you, feel free to give it a ⭐ on GitHub — it really motivates me to keep improving it!”
