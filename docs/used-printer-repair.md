# Used Printer Repairs

Used LP2844 and 2824s have a host of common issues that can be easy to repair with some patience.

## Tools

* Phillips #1 (not 2!) screwdriver at least 3.5cm long.
* Flat blade screwdriver.
* Thermal printer cleaning PADS (not just a pen!)
* Cleaning wipes
* A clean rag
* Q-tips
* Isopropyl alcohol (99.9%, higher than fursuit stuff)

## Cleaning

With cleaning wipes:

* Chassis
* Window
* Spool area

With q-tips and isopropyl alcohol and/or the rag:

* Platten roller
    * See the manual for removal with a flat blade screwriver
* Label dispenser delrin roller (if equipped, the white one)

With the cleaning pads:

* Plug the printer in.
* Place a printer pad as though it's a label to print.
* Press the feed button to feed the cleaning pad through.
* Flip the pad and repeat 4-5 times.

Run the print test several times to check the head status. If you notice blotchy sections that might be a dirty head. If you see missing lines that indicates dead pixels and the head needs replacement.

## Common repairs

Go read the service manual in this directory FIRST BEFORE YOU DO ANY OF THESE PROCEDURES. Some of the procedures are documented, but most importantly there are diagrams of common procedures and parts. This document attempts to stick to that terminology.

### Misalignment of roll holders

The spring-loaded roll holders use a tooth mechanism to stay in alignment. They should expand and contract in sync, hitting the sides of the case at the same time and retracting to the center at the same time.

If they are not perfectly aligned you may see a gap in one of the tracks when the other side is fully retracted towards the center. This can lead to print alignment problems.

1. Remove the lower case with the three screws on the bottom.
2. Remove the circuit board screw, you shouldn't have to remove more than one or two connectors to tilt it to the side.
3. Remove the plastic shield under the circuit board.
3. Slide the spool holders as wide as they'll go and tilt one of them. You should be able to get it to disengage the center tooth gear so that you can pop it back into the right alignment.
4. Ensure the spools are now in perfect sync.
5. Replace the plastic shield.
6. Replace the circuit board, being sure to plug back in any cables you removed.
7. Replace the lower case.

### One print works but subsequent prints don't

If the printer is equipped with a label dispenser check the state of the switch. Pop the dispenser down and look at the switch, see if it's set to I (enabled) or O (disabled). If it's enabled it means the printer is waiting for you to take the label off before it continues printing.

The label dispenser wait-for-pickup should only be enabled when actually using the dispensing functon. The printer will _completely lock up_ while it waits for you to take the label, leading to confusing behavior.

Don't troll yourself.

