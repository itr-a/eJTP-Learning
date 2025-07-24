# MSF Architecture

**Exploit**\
A module that is used to take advantage of vulnerability and is typically paired with a payload.

**Payload**\
Code that is delivered by MSF and remotely executed on the target after successful exploitation. An example of a payload is a reverse shell that initiates a connection from the target system back to the attacker.\
**Types**\
`Non-Staged Payload` : Sent to the target system as is along with the exploit\
`Staged Payload` : Sent to the target in two parts, whereby:\
The first part (stager)\
Contains a payload that is used to establish a reverseconnection back to the attacker, download the second part of the payload (stage) and execute it

**Encoder**\
Used to encode payloads in order to avoid AV detection. For
example, shikata_ga_nai is used to encode Windows payloads.

**NOPS**\
Used to ensure that payloads sizes are consistent and ensure the stability of a payload when executed.

**Auxiliary**\
A module that is used to perform additional functionality like port scanning and enumeration.