# EPL2 General notes

ASCII commands yeeted over the raw socket at the printer. Go read the full docs for more.

## Image buffer basics

The Zebra thermal label printer systems operate off of an image buffer. You draw
into the image buffer various contents like text, barcodes, lines, etc. and the
printer then stamps the buffer onto a label.

As it's not useful for our purposes this document doesn't go over **form mode**,
a more complex mode that allows you to generate a 'template' image buffer and fill
its placeholder values with a stream of basic inputs. Instead we redraw the image
buffer for each label that is printed. Read the full docs for form mode details.

## Printer density and dots

A lot of these commands and fonts are measured in "dots". This is directly related
to the print head resolution, which on LP 2844 printers is 203 dots per inch with a
4 inch wide print head, meaning a total of 812 dots wide. A 2 inch wide label is
therefore 406 dots wide.

You get to calculate the number of dots _tall_ your labels are by using the same
203 DPI and the inch height measurement of your labels. A 1 inch high label has
203 dots to work within. Consider that when laying out your image buffer.

Thermal label printers work by heating up a 'pixel' very quickly to a high temperature
to trigger the ink in the label, and then cooling it down quickly to stop. There
is a reasonable limit to the amount of power draw the entire print head can draw
at once to accomplish this, and the darkness of the output is a function of

* Print speed (faster means less contact time and is lighter)
* `D` density command, higher is darker (and wears out the head faster)
* Number of pixels active at the same time (watt draw limit by print head circuit)

If you print slower you can print darker with a lower density setting, saving
wear on the print head

If you print something full width like a long horizontal bar it's more likely to
have artifacts as the printer can't handle the high power draw of the whole head
at once; avoid this design. Vertical bars don't have this problem.

Experiment with combinations of the print speed and density setting to find a
good balance.

## Commands

Mostly copy/pasted out of the manual, page numbers are for the EPL2_Prog.pdf file.

⚠ Note: For readability command descriptions add spaces between parameters. These
must be omitted when writing. Examples will not add invalid spaces and should work.

### Command Basics

Nearly all commands expect a LINE_FEED character after their parameters. This is
represented in the docs with the ↵ character.

Most of these commands have some more technical details available in the docs
omitted here for brevity.

### Basic Text

#### A ASCII Text — 43

Write text

`A horizontal, vertical, rotation, font, h_multiplier, v_multiplier, reverse, "DATA"↵`

* horizontal - Horizontal start position in dots
* vertical - Vertical start position in dots
* Rotation - 0,1,2,3 for 0,90,180,270 respectively
* Font - Font selection. 1,2,3,4,5 for built-in fonts, see docs for more
* h_multiplier - Horizontal multiplier for the text width. Can be 1-6 or 8
* v_multiplier - Vertical multiplier for the text height. Can be 1-9.
* Reverse - Reverse the image (white text in black box). Can be N for normal or R for reverse

Example:

* `A50,20,0,2,1,1,N,"Hello World!"↵`

#### ; Code Comment Line — 176

Comment out code. The rest of the line is ignored up to the next line feed.

Example:

* `; This is a code comment!↵`

#### P Print — 135

Print whatever is in the image buffer.

`P count,[copies]↵`

The copies parameter is used with counter to print multiple copies of the same counters.

Example:

* `p1↵` prints one label
* `p2,3↵` prints three copies of two labels, 6 labels total.

#### N Clear Image Buffer — 122

Empty the image buffer.

Note: Run printer configuration commands should be issued prior to issuing the N
command to ensure the image buffer is configured properly before running drawing
commands.

Note: Always send a LINE_FEED prior to the N command to ensure there's nothing
sitting in the command buffer.

Example:

* `↵N↵`

### Drawing

#### LE Line Draw XOR — 117

Draw a line XOR'd on top of existing dots, inverting whatever state of those dots.

`LE horizontal_start, vertical_start, horizontal_length, vertical_length↵`

Example:

* `LE50,200,400,20↵`
* `LE200,50,20,400↵`

#### LO Line Draw Black — 118

Draw a black line, overwriting previous dots.

`LO horizontal_start, vertical_start, horizontal_length, vertical_length↵`

Example:

* `LO50,200,400,20↵`
* `LO200,50,20,400↵`

#### LW Line Draw White — 120

Draw a white line, overwriting previous dots.

`LW horizontal_start, vertical_start, horizontal_length, vertical_length↵`

Example:

* `LW50,200,400,20↵`
* `LW200,50,20,400↵`

#### LS Line Draw Diagonal — 119

Draw a horizontal line in black, overwriting previous dots.

`LS horizontal_start, vertical_start, horizontal_length, vertical_length, vertical_end↵`

Example:

* `LS10,10,20,200,200↵`

#### X Box Draw — 168

Draw a box.

`X horizontal_start, vertical_start, thickness, horizontal_end, vertical_end↵`

Thickness is in dots.

Example:

* `X50,200,5,400,20↵`
* `X200,50,10,20,400↵`

### Images

* GG Retrieve Graphics Image 105
* GK Delete Graphic Writes 107
* GM Store Graphic Writes 108
* GW Direct Graphic Write Image 110
* GI Print Graphics Info. — 106

### Media Configuration

#### xa Autosense media — 167

Automatically sense the label and gap size.

`xa↵`

Note: The printer will feed several labels to detect and verify gap and label length.
Once autosense is complete the configuration is preserved through power cycles.
You can open the printer and re-spool the labels after calibration, then feed one
label to return to the ready-to-print position.

Example:

* `xa↵`

#### q Set label width — 137

Set the printable width of the label, in dots.

`q width↵`

Note: This command will automatically set the left margin and update the reference
point accordingly. It will then reformat the image buffer.

Example:

* `q406↵` - Set the printable area for a 2 inch wide label

* Q Set Form Length
* R Set Reference Point Stored 143
* Z Print Direction Stored 170
* fB Adjust Backup Position Writes 99
* JB Disable Top Of Form Backup Stored 114
* JC Disable Top Of Form Backup - All Cases Stored 115
* JF Enable Top Of Form Backup Stored 116

### Printer configuration

* U Print Configuration — 148

#### U Print Configuration  148

Print the configuration onto a label.

`U↵`

Example:

* `U↵` - Print the current configuration onto a label.

#### D Density  88

Set the print density (darkness).

Note: This controls the temperature of the print head. Higher temperatures can lead to distorted images, sticking of the label to the print head, and decreased head lifetime. **You should use the lowest necessary print density for your labels to get a good image.**

`D density↵`

* density - 0-15 where 0 is the lightest and 15 is the darkest. Usually 8 is fine.

Example:

* `D8↵` - Set print density to 8.

#### S Speed Select  144

Set the print speed

`S speed↵`

The speed table for other printer models should be consulted in the EPL manual.

For the LP2824 and 2844 the settings are

* 1 - 1.5 inches per second
* 2 - 2.0 inches per second
* 3 - 2.5 inches per second
* 4 - 3.5 inches per second

Note: The faster the print speed the lighter the image. Speed and density should be balanced for a good produced image.

Example:

* `S4↵` - Set the speed to 3.5 inches per second


* dump Enable Dump Mode — 89
* M Memory Allocation Writes 121
* o Cancel Software Options Writes 123
* O Hardware Options Stored 132
* ^@ Reset Printer — 173
* ^default Set Printer to Factory Defaults Writes 174

### Printer Errors

* eR User Definable Error Response Writes 92
* US Enable Error Reporting Stored 159
* UT Enable Alternate Error Reporting Stored 161
* ^ee Status Report - Immediate — 175

### Printer Time

* TD Date Recall & Format Layout Writes 145
* TS Set Real Time Clock Stored 146
* TT Define Time Layout (& Print Time) Writes 147

### Printer Communication

* r Set Double Buffer Mode Stored 142
* Y Serial Port Setup Stored 169
* W Windows Mode Stored 166
* oM Disable Initial Esc Sequence Feed Stored 128
* UA Enable Clear Label Counter Mode Session 149
* UB Reset Label Counter Mode Writes 150
* UE External Font Information Inquiry — 151
* UF Form Information Inquiry — 152
* UG Graphic Information Inquiry — 153
* UI Host Prompts/Codepage Inquiry Session 154
* UM Codepage & Memory Inquiry Session 155
* UN Disable Error Reporting Stored 156
* UP Codepage & Memory Inquiry/Print — 157
* UQ Configuration Inquiry — 158
* OEPL1 Set Line Mode Writes 134
    * Transmissive (Gap) Sensor
    * Black Line Sensor|
    * Continuous Stock

### Cutting

* C Cut Immediate — 87
* f Cut Position Stored 98

### Barcodes

* B Bar Code Image 52
* B RSS-14 Bar Code Image 58
* b Aztec Image 62
* Aztec Mesa Image 66
* Data Matrix Image 68
* MaxiCode Image 72
* PDF417 Image 76
* QR Code Image 83
* oB Cancel Customize Bar Code Writes 124
* oW Customize Bar Code Parameters Writes 130
* oH Macro PDF Offset Image 126

### Fonts

* EI Print Soft Font Info. — 90
* EK Delete Soft Font Writes 91
* ES Store Soft Font Writes 93
* oE Line Mode Font Substitution Writes 125
* oR Character Substitution (Euro) Writes 129

### Forms

* AUTOFR Automatic Form Printing Form 50
* C Counter Form 85
* FE End Form Store Writes 100
* FI Print Form Info. — 101
* FK Delete Form Writes 102
* FR Retrieve Form — 103
* FS Store Form Writes 104
* PA Print Automatic Form 136
* ? Download Variables Form 172
* V Define Variable Form 164

### Character sets

* i Asian Character Spacing Stored 111
* I Character Set Selection Stored 112
