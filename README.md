# axiompro
Test Axiom Pro MIDI Input and Output Data

This code uses WebMidi to perform MIDI data experiments with the Axiom Pro 61. The code must run in a browser that is capable of using WebMidi. In my case I use Google Chrome on Mac OS Monterey.

## Online Documentation
These online PDF documents provide a lot of info about how Axiom Pro's HyperControl works with DAWs.

[Live](https://kb.inmusicbrands.com/media/files/HC_Ableton_2009Aug24_EN01.pdf)
[Pro Tools](https://kb.inmusicbrands.com/media/files/081211_HCProTools_UG_EN01.pdf)
[Logic](https://kb.inmusicbrands.com/media/files/081210_HCLogic_UG_EN02.pdf)
[Reason](https://kb.inmusicbrands.com/media/files/HyperControl%20for%20Reason%20EN.pdf)
[Cubase](https://kb.inmusicbrands.com/media/files/HC_Cubase_2009May01_EN01.pdf)

And this online python script provides info how sysex messages are used with Axiom Pro:

[Python Script](https://github.com/gluon/AbletonLive9_RemoteScripts/blob/master/AxiomPro/AxiomPro.py)

## HyperControl - Test 1
Test 1 sends a sysex identity request and displays received data.

#### Observations
As expected, an identity reply is received from the device.

The message received from the device is:
```
sysex f0 7e 7f 6 2 0 1 5 63 e 22 40 30 31 32 31 f7
```

#### Conclusions
The identity request contains these values
```
ID  0x00, 0x01, 0x05
Family Code  0x63(99), 0x0e(14)
Model Number  0x22(32), 0x40(64)
Version  0x30, 0x31, 0x32, 0x31
```