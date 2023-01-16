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
sysex 0xf0 0x7e 0x7f 0x6 0x2 0x0 0x1 0x5 0x63 0xe 0x22 0x40 0x30 0x31 0x32 0x31 0xf7
```

#### Conclusions
The identity request contains these values
```
ID  0x00, 0x01, 0x05
Family Code  0x63(99), 0x0e(14)
Model Number  0x22(32), 0x40(64)
Version  0x30, 0x31, 0x32, 0x31
```

## HyperControl - Test 2
Test 2 emulates the "refresh" code in AxiomPro.py. Note that the refresh_state() code sets a boolean variable, waiting_for_first_response = true, and then sends a sysex message with values [32, 46]. And then note that handle_sysex() looks sysex message data that starts with 32, and detects if the message is "is_setup_response" by looking for 46 or 38.  If it is, it sets sets waiting_for_first_response = false and sends a couple sysex messages. This test emulates that because it appears to be some sort of initialization/setup protocol.

#### Observations

1. After sending the "refresh" sysex message with values [32, 46], this sysex message is received from the device:
```
240 0 1 5 32 127 32 46 247
```

2. After sending the the sysex message with data of [17, 3, 0, 1, 65, 98, 108, 101, 116, 111, 110, 32, 76, 105, 118, 101, 32, 67, 111, 110, 116, 114, 111, 108, 32, 0, 1, 4, 83, 117, 114, 102, 97, 99, 101, 32, 118, 49, 46, 48, 46, 48, 46], the screen on the device displays:
```
Ableton Live Control Surface v 1.0.0.
```

Note that the ascii values of these data bytes:
[65, 98, 108, 101, 116, 111, 110, 32, 76, 105, 118, 101, 32, 67, 111, 110, 116, 114, 111, 108]
are the ASCII equivalent of "Ableton Live Control"
and the data bytes:
[83, 117, 114, 102, 97, 99, 101, 32, 118, 49, 46, 48, 46, 48, 46]
are the ASCII equivalent of:
"Surface v1.0.0.0."

#### Conclusions
1. This seems to be an initialization/setup protocol where the host sends an initializae sysex to the device and it responds with the same message to acknowledge it. The message is two bytes, [32 46].

2. Lines of text can be sent by a sysex message.

3. Before sending the text display message a simple message with data [16] is sent to it and I don't know what that is for.




