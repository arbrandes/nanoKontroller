nanoKontroller
==============

This is a slightly modified version of [tpneill/nanoKontroller](https://github.com/tpneill/nanoKontroller) with support for controlling individual programs added and a few other things changed to meet my needs.

A very simplistic Python app to turn the controllers on a Korg nanoKONTROL2 device into user actions on Linux.

Config is controlled via a mapping file that ties inputs on the device to various events.

LED control will not work until the magic control sequence has been sent to the device. Use the Windows control software to switch to this mode.

Features
========

* Maps any button to a keypress on the host keyboard via evdev events
* Maps sliders or knobs to the volume of any audio device, including support for volume limiting or overdrive
* Maps any button to the mute control for any audio device
* Maps any button to an external shell command that includes the device key and value

Requires
========

* PulseAudio for audio control
* MIDO for MIDI control
* evdev for keyboard events

Config
======
By default, config should be in ```~/.config/nanoKontroller.ini``` but this can be overridden by the ```-c```/```--config``` command line argument.

The ```[general]``` section sets the name pattern of the nanoKontrol MIDI device and the LED mode (are they on ```toggle``` or ```default``` behaviour when you press them? This can be changed in the Korg Windows Software). The script will search for a MIDI devices that matches the specified pattern and take the first one.

The ```[audioinputs]``` section defines audio devices to use as inputs, such as headsets or microphones. Similarly, ```[audiooutputs]``` defines audio devices to use as outputs, such as headsets or speakers. To help finding these names there is a ```--list-devices``` command line option to print them to the console.

The ```[applications]``` section defines the application names to use as inputs. To help find these names, the audio sources of the program you want to find out have to be playing, and then call nanoKontroller with the ```--list-programs``` command line option.

The ```[keymap]``` section defines what to do with each button on the device.

Bare options such as ```KEY_PLAYPAUSE``` are taken directly from evdev as a fake keypress to generate.

Options prefixed with ```mute``` such as ```mute/mainOutput``` are used to tie a button to mute an audio device. In this case, the device ```mainOutput``` which maps to ```alsa_output.usb-0d8c_Generic_USB_Audio_Device-00.iec958-stereo```

Options prefixed with ```default-sink``` such as ```default-sink/mainOutput``` are used to tie a button to set an audio device as the default sink. In this case, the device ```mainOutput```, which maps to ```alsa_output.usb-0d8c_Generic_USB_Audio_Device-00.iec958-stereo```.

Options prefixed with ```program-mute``` such as ```program-mute/chrome``` are used to tie a button to mute a program. In this case, the program ```chrome``` which maps to ```Google Chrome```. This option supports muting multiple programs, just list them as a comma-separated list: ```program-mute/spotify,media_player```

Options prefixed with ```volume``` such as ```volume/mainOutput``` are used to tie a slider or knob to the volume of an audio device, in this case the device ```mainOutput```. An optional third argument can be supplied like ```volume/mainOutput/80``` to limit the maximum volume to 80% or ```volume/mainOutput/150``` to boost the maximum volume to 150%.

Options prefixed with ```program-volume``` such as ```program-volume/chrome``` are used to tie a slider or knob to the volume of a program, in this case the device ```chrome```. An optional third argument can be supplied like ```volume/chrome/80``` to limit the maximum volume to 80% or ```volume/chrome/150``` to boost the maximum volume to 150%. This option supports changing the volume for multiple programs, just list them as a comma-separated list: ```program-volume/spotify,media_player```

Options prefixed with ```exec``` are used to tie a button to executing a shell command. The values ```{NK_KEY_ID}``` and ```{NK_KEY_VALUE}``` are substituted by the device key ID number and the value (0 for button release, 127 for button press).

Options prefixed with ```cycle-default-sink``` such as ```cycle-default-sink/mainOutput,headsetOutput``` are used to tie a button to cycle between the specified audio devices as the default sink.  It is possible to specify more than two devices.


Example Config
==============
```
[general]
nanoKONTROLPattern = nanoKONTROL2
ledMode = toggle

[audioinputs]
headsetInput = alsa_input.usb-Plantronics_SupraPlus_Wideband_USB-00.analog-mono

[audiooutputs]
headsetOutput = alsa_output.usb-Plantronics_SupraPlus_Wideband_USB-00.analog-stereo
mainOutput = alsa_output.usb-0d8c_Generic_USB_Audio_Device-00.iec958-stereo

[applications]
spotify = spotify
chrome = Google Chrome
media_player = Videos

[keymap]
PLAY = KEY_PLAYPAUSE
PREV = KEY_PREVIOUSSONG
NEXT = KEY_NEXTSONG
STOP = KEY_STOPCD
RECORD = KEY_RECORD
CYCLE = cycle-default-sink/mainOutput,headsetOutput
PARAM1_SLIDER = volume/mainOutput
PARAM1_MUTE = mute/mainOutput
PARAM1_RECORD = default-sink/mainOutput
PARAM2_SLIDER = volume/headsetOutput/150
PARAM2_MUTE = mute/headsetInput
PARAM2_RECORD = default-sink/headsetOutput
PARAM3_SOLO = exec/echo "Key {NK_KEY_ID}, value {NK_KEY_VALUE}"
PARAM4_SLIDER = program-volume/spotify,media_player
PARAM4_MUTE = program-mute/spotify,media_player
PARAM5_SLIDER = program-volume/chrome
PARAM5_MUTE = program-mute/chrome
