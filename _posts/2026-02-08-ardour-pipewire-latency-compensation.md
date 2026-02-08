## Latency compensation in Ardour with PipeWire

1. Start Ardour 9.0 or newer. This tutorial is for Pipewire > 0.5. To check what version you have use this command: `wireplumber --version`
2. Start `qjackctl`.
3. Run `wpctl status -n` in console. Copy ID of your input & output devices.
4. Run `jack_iodelay` in another console tab.
5. Physically connect output of your audio interface to input with some cable.
6. Go to `qjackctl` and open graph. Connect iodelay node to your audio interface input & output.
7. When something was connected incorrectly you will see `Signal below threshold...` in the output of `jack_iodelay`
8. If all is good you will see this:

```
Signal below threshold...
  3695.549 frames     76.991 ms total roundtrip latency
	extra loopback latency: 879 frames
	use 439 for the backend arguments -I and -O
```

Note down number 439 (will be different for you)

9. Create new file `~/.config/wireplumber/wireplumber.conf.d/audiolink.conf ` with following content:

```
monitor.alsa.rules = [
  {
    matches = [
      {
        node.name = "alsa_output.usb-Burr-Brown_from_TI_USB_Audio_CODEC-00.analog-stereo"
      }
    ]
    actions = {
      update-props = {
        # Set latency compensation in samples
        # Adjust this value based on results from jack_iodelay
        latency.internal.rate = 439
      }
    }
  }
  {
    matches = [
      {
       	node.name = "alsa_input.usb-Burr-Brown_from_TI_USB_Audio_CODEC-00.analog-stereo"
      }
    ]
    actions = {
      update-props = {
        # Set latency compensation in samples
        # Adjust this value based on results from jack_iodelay
        latency.internal.rate = 439
      }
    }
  }
]
```

