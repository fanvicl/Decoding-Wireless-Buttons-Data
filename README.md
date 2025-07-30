# Decoding-Wireless-Buttons-Data
Decoding wireless signaling format sent by Manchester encoding used by many vendors such as Fanvil, Welltech rFbyte etc.
In most cases is used A7329 (AMICCOM) a low-power, 433mHz RF transceiver often used in wireless button and remote control systems.
A7329 is a general-purpose transceiver—the actual packet structure (encoding, ID, button code, etc.) is defined by the firmware of the microcontroller that communicates with the A7329.

There is no universal “A7329 code format”—the encoding is custom, defined by the designer.
The 4-byte (8 hex digit) codes you see on your web interface are probably the payload transmitted by the microcontroller, not the chip’s own protocol.
Each button/remote likely has a unique ID, and each button sends a code with a fixed structure (often a device ID + button code).
Sometimes there is a simple checksum or counter for basic replay protection, but usually not for cheap button systems

1. Format and Context
Each code is 8 hex digits (4 bytes, or 32 bits), for example:

0202e4e9 

0000b14b 

0202e6ef 

000003a7 


First 2 or 3 bytes = ID, last byte = button code or button code + rolling counter.
Or: 3 bytes ID + 1 byte data.

# Example Mapping
Let’s see your sample data, with each byte split:
0202e4e9 → 02 02 E4 E9  

0000b14b → 00 00 B1 4B   

0202e6ef → 02 02 E6 EF   

000003a7 → 00 00 03 A7 


Group 1: 02 02 XX XX 

Group 2: 00 00 XX XX 

If "02 02" is one remote and "00 00" is another, the next bytes might be button and rolling code or a CRC.

What is encoded?
Each button sends a fixed, unique 32-bit code.
This is the simplest “addressed remote” scheme.

There is no ID/button separation visible, and no rolling counter or CRC.
The system only cares about the exact 4-byte code for each button.
This is called “fixed code” encoding (common for basic wireless buttons).

Each code is just a 32-bit number (presented in hex).
The meaning of the bytes (e.g., which part is button, which part is remote ID) is only relevant if you have multi-button remotes or want to clone/create new buttons.

The firmware in the Fanvil button simply transmits a pre-programmed value per button.
Simple logic IC assigning the code at manufacturing.

No special decoding required.
Already have the “decoded” values.

0202e4e9 → Button 1

0000b14b → Button 2

0202e6ef → Button 3

000003a7 → Button 4


To emulate a FAnvil button, transmit the same 32-bit code from another compatible device.
To clone, copy the code to another transmitter or microcontroller with the same wireless stack.

If You Need to Generate or Recognize the Code in Software:
# Example in Python
python
Copy
Edit
button_codes = {
    "Button1": "0202e4e9",
    
    "Button2": "0000b14b",
    
    "Button3": "0202e6ef",
    
    "Button4": "000003a7",
    
}

def get_button(code):
    for name, val in button_codes.items():
        if val == code.lower():
            return name
    return "Unknown"

# Example usage:
print(get_button("0202e4e9"))  # Output: Button1
Summary Table:

Button	Code (Hex)	Code (Binary)

Button 1	0202e4e9	00000010 00000010 11100100 11101001

Button 2	0000b14b	00000000 00000000 10110001 01001011

Button 3	0202e6ef	00000010 00000010 11100110 11101111

Button 4	000003a7	00000000 00000000 00000011 10100111


No rolling, no cryptography, no advanced protocol—just fixed 32-bit codes per Fanvil button.
All you need to do is map each code to each button in your application or script.
