# Cheat Sheet for IEC 104 Protocol

## What is IEC 104?
IEC 60870-5-104 (IEC 104) is a telecontrol network protocol used in energy distribution systems for SCADA and station communication.

## IEC 104 Frame Formats
There are three different control field formats:
1. **U-Format**
  - consists of an APCI only
  - is used for controlling the connection: start, stop, test frames
  - each U-Format frame is sent as an "ACT" = activation and must be responded with a "CON" confirmation 
3. **S-Format**
  - consists of an APCI only
  - is only used for acknowledging I-Format frames
5. **I-Format**
  - contains information (one or multiple ASDU)
  - includes Sent and Receive Sequence Numbers

## APDU
A whole IEC 104 frame is called an APDU (Application Protocol Data Unit)

An APDU consists of
  - an APCI (Application Protocol Control Information)
  - (if it is an I-Format): one or multiple ASDU (Application Service Data Unit)

**APDU = APCI + n * ASDU**

## APCI
An Apci consits of six bytes.

 - Byte 1: Start Byte (0x68)
 - Byte 2: Length of APDU (max. 253)
 - Byte 3 .. 6: Control field byte 1 .. 4

### U-Format

 - **Byte 1:** Bit 8: TESTFR con, Bit 7: TESTFR act, Bit 6: STOPDT con, Bit 5: STOPDT act, Bit 4: STARTDT con, Bit 3: STARTDT act, Bit 2: 1, Bit 1: 1
 - **Byte 2** Bit 8 .. Bit 1 : 0
 - **Byte 3** Bit 8 .. Bit 1 : 0
 - **Byte 4** Bit 8 .. Bit 1 : 0

Each U-Format frame can only have one function, e.g. STARTDT con = 0x0B, 0x00, 0x00, 0x00

Functions are
- **TESTFR act:** Send a test frame to check connection (heart beat)
- **TESTFR con:** Confirmation response to a test frame activation
- **STOPDT act:** Stop data transmission
- **STOPDT con:** Confirmation response of stop of data transmission
- **STARTDT act:** Start data transmission
- **STARTDT con:** Confirmation response of start data transmission


### S-Format

If there are no I-Format frames to be sent, an S-Format frame can be used to acknowledge received I-Format frames.

 - **Byte 1:** Bit 8 .. Bit 2: 0, Bit 1: 1
 - **Byte 2** Bit 8 .. Bit 1 : 0
 - **Byte 3** Bit 8 .. Bit 2: RSN (LSB) Bit 1 : 0
 - **Byte 4** Bit 8 .. Bit 1 :(MSB) RSN
 
 
 ### I-Format
Each I-Format frame consists of an APCI and one or multiple ASDU.
The I-Format APCI consists of two sequence numbers: RSN and SSN.

 - **Byte 1:** Bit 8 .. Bit 2: SSN (LSB), Bit 1: 0
 - **Byte 2** Bit 8 .. Bit 1 : (MSB) SSN
 - **Byte 3** Bit 8 .. Bit 2: RSN (LSB) Bit 1 : 0
 - **Byte 4** Bit 8 .. Bit 1 :(MSB) RSN
 

### Sequence Numbers
Each I-Format frame has a send sequence number (SSN) and a receive sequence number (RSN), which consist of 15 bits each: 0 .. 32767.

For each sent I-Format frame the SSN is incremented by one.
The RSN of the I-Format frame equals the last SSN (plus one) that was received by the communication instance sending the I-Format frame (RSN is one ahead).
A RSN acknowledges all frames before its number (RSN = 7 means 6 ... 0 are also acknwoledged.

If one communication instance does not send I-Format frames, it must use S-Format frames for acknowledgment.

Acknowledgment must be done within timeout t2.

### TCP Port
TCP Port is 2404
