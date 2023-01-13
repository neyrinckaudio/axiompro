# axiompro
Test Axiom Pro MIDI Input and Output Data

This code uses WebMidi to perform MIDI data experiments with the Axiom Pro 61. The code must run in a browser that is capable of using WebMidi. In my case I use Google Chrome on Mac OS Monterey.

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
Family Code  0x63, 0x0e
Model Number  0x22, 0x40
Version  0x30, 0x31, 0x32, 0x31
```