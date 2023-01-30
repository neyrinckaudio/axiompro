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

2. After sending the the sysex message with data of [17, 3, 0, 1, 65, 98, 108, 101, 116, 111, 110, 32, 76, 105, 118, 101, 32, 67, 111, 110, 116, 114, 111, 108, 32, 0, 1, 4, 83, 117, 114, 102, 97, 99, 101, 32, 118, 49, 46, 48, 46, 48, 46], the screen on the device displays on line 2:
```
Ableton Live Control
```
and on line 3:
```
Surface v 1.0.0.
```
and both are centered.

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

## HyperControl - Test 2.1

This test doesn't run anything here. Instead it observes MIDI messages when Logic Pro launches with the Axiom Pro 61 connected to it. And when Logic Pro quits.

Logic Pro 10.7.4
Macbook Pro Retina, Intel processor
Mac OS Monterey 12.2.1
Midi Monitor v 1.4.1

#### Observations
When Logic Pro is launched, Midi Monitor 2 observes these messages:
```
12:30:06.397	To HyperControl Out  	SysEx		M-Audio (Midiman) $9 bytes	F0 00 01 05 20 7F 20 2A F7
12:30:06.397	To HyperControl Out  	SysEx		M-Audio (Midiman) $8 bytes	F0 00 01 05 20 7F 10 F7
12:30:06.403	From HyperControl In   	SysEx		M-Audio (Midiman) $9 bytes	F0 00 01 05 20 7F 20 2A F7
12:30:07.401	To HyperControl Out  	SysEx		M-Audio (Midiman) $1F bytes	F0 00 01 05 20 7F 11 03 01 01 57 65 6C 63 6F 6D 65 20 74 6F 20 4C 6F 67 69 63 20 50 17 12 02 F7
```
12:30:06.397 Note that Logic first sends a message with data [32, 42]. 
12:30:06.397 Logic immediately sends a message with data [16].
12:30:06.403 The device replies with data [32, 42].
12:30:07.401 Logic sends a message that has ASCII data, "Welcome To Logic Pro"

This is very similar to the Ableton code. It differs a bit:

1. Instead of send/reply [32 46] the send/reply is [32 42]
2. The mystery [16] message is sent before receiving the [32 42] reply.

And the device shows the text "Welcome To Logic Pro" on line 2, centered.

When Logic Pro is quit, Midi Monitor 2 observes these messages:
```
14:05:23.999	To HyperControl Out  	SysEx		M-Audio (Midiman) 19 bytes	F0 00 01 05 20 7F 11 01 00 00 20 20 20 20 20 20 20 20 F7
14:05:23.999	To HyperControl Out  	SysEx		M-Audio (Midiman) 19 bytes	F0 00 01 05 20 7F 11 01 00 0A 20 20 20 20 20 20 20 20 F7
14:05:23.999	To HyperControl Out  	SysEx		M-Audio (Midiman) 8 bytes	F0 00 01 05 20 7F 10 F7
14:05:24.000	To HyperControl Out  	SysEx		M-Audio (Midiman) 27 bytes	F0 00 01 05 20 7F 11 03 01 03 4C 6F 67 69 63 20 50 72 6F 20 63 6C 6F 73 65 64 F7
14:05:24.020	From HyperControl In   	SysEx		M-Audio (Midiman) 9 bytes	F0 00 01 05 20 7F 20 00 F7
```
14:05:23.999 Logic sends a sysex message with ASCII data of "        "
14:05:23.999 Logic sends a sysex message with ASCII data of "        "
14:05:24.000 Logic sends a sysex message with data [16]
14:05:24.000 Logic sends a sysex message with ASCII data of "Logic Pro closed"
14:05:24.020 Logic sends a sysex message with data [32, 0]

The device screen very briefly displays "Logic Pro closed"

#### Conclusions

Logic uses a very simmilar initialization protocol to that coded for Ableton. It also shows a disconnect protocol. Curiously the initialize message data is [32, 42] instead of [32, 46].

## HyperControl - Test 3
Test 3 emulates the "disconnect" code in AxiomPro.py. Notice how it sends a sysex with data [32, 0] just like Logic does. And it sends the mysteriosu sysex with data [16]. And it sends a sysex with ASCII values of "Ableton Live Control" and "Surface Closed." Run this test after running Test 2 to emulate a connect/disconect cycle in Ableton.

#### Observations
1. This sysex message is received from the device:
```
240 0 1 5 32 127 32 0 247
```

2. "Ableton Live Contr" is briefly displayed on the device. The "ol" portion sent seems to be cut off on the right of the display.

3. "Surface Closed." is briefly displayed on the device.

4. After briefly displaying "Ableton Live Contr" and "Surface Closed" the device display changes to showing the Midi controller info it normally shows when first poered up.

#### Conclusions
It looks like sysex with data [32, 0] is a disconnect message and the device acknowledges with the same message to indicate it is disconnected.

The format of the text "Ableton Live Control" is cut off on the right so it seems the formatting of how it is displayed must be done carefully. perhaps Logic is not formatting the text correctly.


## HyperControl - Test 4
This test sends various display sysex messages as defined in AxiomPro.py to see what the Axiom device display shows. The init() function was added and parts of the Python init function were ported:
track_display
device_display
parameter_display
page_displays

I added a button labeled "Run Test 4" that invokes sending messages to the displays.

#### Observations
I see "Trk Disp   Dev Disp" across the top in a large font. I see "Param Disp" in the second row from the bottom. I see "Pg1  Pg2  Pg3  Pg4  " in the bottom row.

track_display - top row left, large variable-width font
device_display - top row right, large variable-width font
parameter_display - second from bottom row, fixed width font
page_displays[0] - bottom row, column 0, fixed width font, aligned with display button 1
page_displays[1] - bottom row, column 4, fixed width font, aligned with display button 2
page_displays[2] - bottom row, column 9, fixed width font, aligned with display button 3
page_displays[3] - bottom row, column 14, fixed width font, aligned with display button 4

The sysex "commands" for these are:
track_display - 17,1,0,0
device_display - 17,1,0,10
parameter_display - 17,2,0,0
page_displays[0] - 17,4,0,0 
page_displays[1] - 17,4,1,0 
page_displays[2] - 17,4,2,0 
page_displays[3] - 17,4,3,0 

#### Conclusions
17,1,0,C is for the top row and C addresses the starting point.
17,2,0,0 is for the second from bottom row starting at the left.
17,4,B,0 is for the bottom row and B addresses the button alignment

## HyperControl - Test 5
The AxiomPro.py code specifies sysex data for a "self._track_display.set_clear_all_message." The data is [16] which is the same as the mysterious message referenced in earlier tests. This test tries that message.

#### Observations
Procedure 1
1. Connect - "Ableton Live Control Surface v 1.0.0." is displayed.
2. Run Test 5 - The above text is cleared.

Procedure 2
1. Connect - "Ableton Live Control Surface v 1.0.0." is displayed.
2. Run Test 4 - "Trk Disp   Dev Disp", "Param Disp", and "Pg1  Pg2  Pg3  Pg4  " are displyed.
3. Run Test 5 - All text is cleared.

#### Conclusions
The sysex message of [16] clears the entire display.

## HyperControl - Test 6
The AxiomPro.py code specifies sysex data for a "encoder_display." This test tries that message. It starts with [17,3] which is what the sysex messages are in "refresh_state" and "disconnect". The area of the text displayed for "refresh_state" and "disconnect" shows graphics of encoders when disconnected, so it seems that is the "encoder_display" area.

AxiomPro.py sets up eight "segments", each with a diffring set of data that preceds the ASCII values.

#### Observations
The eight different segments are displayed as two across each of four rows that encompass the encoder_display area. Segments 1-4 are at the start of the four rows. Segments 5-8 are at the middle of the four rows. 

The data preceding each text segment is:
segment 0 - [0, 0]
segment 1 - [0, 1, 0]
segment 2 - [0, 2, 0]
segment 3 - [0, 3, 0]
segment 4 - [0, 0, 13]
segment 5 - [0, 1, 13]
segment 6 - [0, 2, 13]
segment 7 - [0, 3, 13]

For the setup/initialize text displayed, it has two portions with data:
"Ableton Live Control" - [0, 1]
"Surface v1.0.0." - [0, 1, 4]

For the disconnect text displayed, it has two portions with data:
"Ableton Live Control" - [0, 4]
"Surface Closed." - [0, 1, 4]

#### Conclusions
17,3 addresses the encoder display area.

For segment 0
[R, C] - R addresses rows 1-4. C addresses the column

For segments 1-7
[0, R, C] - R addresses rows 1-4. C addresses the column

## HyperControl - Test 7
I started experimenting with encoder display messages. Ableton organized them as eight segments, two on each line at fixed column positions. However, the refresh and disconnect protocols show that the display can be addressed in other ways using row and column. As I experimented I realized the encoder_display code hard-codes zero values that mark the end of segments. I constructed a new function, encoderDisplays2, that uses row and column info passed into it and terminates segments with a zero. I created five different sub-tests:

7a - Display "Hello" and "World" at separate rows and columns.
7b - Display text at each row and incrementing columns for each.
7c - Display "0123456789" at each row and incrementing columns for each, starting at the top row.
7d - Display "0123456789" at each row and incrementing columns for each, starting at the bottom row.
7e - Display 12 separate strings at various locations.

#### Observations
Each time a sub-test is run, the entire encoder display refreshes and displays nothing except the text passed in to the message.

Characters are rendered with a fixed-width font that is 6 pixels wide.

For 7b the column locations does not look quite as expected. I expected the column value to match up with the fixed-width font width.

Tests 7c and 7d make it easy to the unexpected column locatiosn to be consistent no matter what order items are displayed.

#### Conclusions
17,3 addresses the encoder display area.

Any number of text segments can be "printed" in a message.

Each segment is addressed by a row and column and is terminated with a 0 value.

[R, C] - R addresses rows 1-4. C addresses the column

The column value sets the position a multiple of 5 pixels. Thus, text characters on one line do not align with characters on another line unless they use the same column value because the font is six pixels wide. That is probably a bug in the Axiom Pro device firmware. And I doubt there is any way to truly center text unless it is padded left with spaces.

## HyperControl - Test 8
In this test we press buttons, move sliders, and move encoders and observe messages sent by the device. It is important to Connect before using the controls so that the device will send them.

#### Observations
Buttons, sliders, and encoders all send conntroller messages. The controller number identifies the control.

Button down sends value 0x7f.
Button up sends value 0.
Sliders send values 0x0 - 0x7f with 0x0 at bottom and 0x7f at top.
Encoders, when turned to right,  send a small value such as 1, 2, or 3 depending how fast it is turned.
Encoders, when turned to left,  send a large value such as 0x7f, 0x7e, or 0x7d depending how fast it is turned.


#### Conclusions
Buttons
Buttons are momentary.

Controller Numbers
Soft Key 1 - 74
Soft Key 2 - 75
Soft Key 3 - 76
Soft Key 4 - 77
Peek - 78
Mode - 109
Track Left - 110
Track Right - 111
Mute - 12
Solo - 13
Bank Left - 14
Bank Right - 15
S10 - S18 - 49,50,51,52,53,54,55,56,57
Loop - 113
Rewind - 114
Forward - 115
Stop - 116
Play - 117
Record - 118

Sliders
Sliders send absolute position with 7 bits precision.
Controller Numbers
S1 to S9 - 33,34,35,36,37,38,39,40,41

Encoders
Encoders use twos-complement for negative (left) values with 7 bits precision. 
Controller Numbers
E1 to E8 - 17,18,19,20,21,22,23,24

## HyperControl - Test 9
As the user guides for DAWs explain, you can switch the device between "Transport" and "Normal" mode. This is done by pressing Edit and then using the 0 keypad button.

#### Observations
When disconnected, the device sends a message each time 0 is pressed:

sysex 240 0 1 5 32 127 32 1 247 (when Transport mode is selected)

sysex 240 0 1 5 32 127 32 0 247 (when Normal mode is selected)

When connected, the device sends a message each time 0 is pressed:

sysex 240 0 1 5 32 127 32 47 247 (when Transport mode is selected)

sysex 240 0 1 5 32 127 32 46 247 (when Normal mode is selected)

#### Conclusions
The [32, X] message has more possible values.

For Ableton:
[32, 0] - disconnected, normal mode
[32, 1] - disconnected, transport mode
[32, 46] - connected, normal mode
[32, 47] - connected, transport mode

## HyperControl - Test 10
Here I test the mode switching while connected to Logic.

#### Observations
16:36:46.499	From HyperControl In   	SysEx		M-Audio (Midiman) 9 bytes	F0 00 01 05 20 7F 20 2A F7 (normal mode selected)
16:36:47.794	From HyperControl In   	SysEx		M-Audio (Midiman) 9 bytes	F0 00 01 05 20 7F 20 2B F7 (transport mode selected)

#### Conclusions
[32, 0] - disconnected, normal mode
[32, 1] - disconnected, transport mode
[32, 46] - connected to Ableton, normal mode
[32, 47] - connected to Ableton, transport mode
[32, 42] - connected to Logic, normal mode
[32, 43] - connected to Logic, transport mode

## HyperControl - Test 11
This test experiments with connecting by trying several values besides 46 and 42.

#### Observations
If I connect with 40, 44, or 48, the device responds with an acknowledge, but the device doesn't behave like it is in normal hypercontrol mode in a consistent way. In fact, it is difficult to pin down what is going on. But one thing is for sure, if I press Edit and then Exit, the four display buttons change to non-normal mode behavior. As well, the encoders don't send. As if it is another mode. 

38 and 50 seem to be mostly normal, but the sliders and slider buttons don't send values. 

42,46,50 do behave normal.

#### Conclusions
Normal mode appears to be in multiples of 4. Transport mode is normal + 1. The other two values might be separate modes. Maybe 50 is for a different DAW.
