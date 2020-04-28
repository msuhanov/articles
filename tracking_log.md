# Basic information

* The *tracking.log* file is located in the *\System Volume Information* directory.
* This file contains a *MoveTable*. According to Microsoft, *the MoveTable has an entry for each file that has been moved off the volume, where the file had an ObjectID.*
* The first sector is used by a header. All subsequent sectors are called "log sectors".
* A log sector contains multiple log entries stored without gaps between them (each log entry must end within the same sector).
* Every log sector ends with a sector footer.
* A sector size can be either 512 bytes or 4096 bytes.

# Header

Offset (bytes)|Length (bytes)|Meaning
---|---|---
0|16|Log signature (GUID, as bytes: EC A7 43 66 FE EF D1 11 B2 AE 00 C0 4F B9 38 6D)
16|4|Unknown (65536)
20|4|Flags (0x1 - log is flushed)
24|4|Unknown
28|4|Expansion data: lowest log entry index present before log expansion
32|4|Expansion data: highest log entry index used before log expansion
36|4|Expansion data: file size before log expansion
40|92|Extended header

## Extended header

The first 64 bytes are called volume information.

Offset (bytes)|Length (bytes)|Meaning
---|---|---
0|16|Machine ID
16|16|Volume object ID
32|8|Unknown (0)
40|8|Unknown timestamp (FILETIME)
48|8|Unknown timestamp (FILETIME)
56|4|Flag (if non-zero, make object IDs reborn)
60|4|Unknown state
64|4|Unknown log entry index
68|4|Unknown log entry index
72|4|Unknown log entry index
76|4|Unknown log entry index
80|4|Unknown log entry index
84|4|Unknown log entry index
88|4|Unknown log entry index

# Log entry

Offset (bytes)|Length (bytes)|Meaning
---|---|---
0|4|Next log entry index (zero-based)
4|4|Previous log entry index (zero-based)
8|4|Log entry type: 1 - unused (fields below are set to zero), 2 - move notification
12|4|Log entry index (zero-based)
16 |4 |Unknown (0)
20|16 |Object ID
36 |16 |DROID [1] (volume field)
52|16|DROID (object field)
68|16|Machine ID
84|16|Birth DROID (volume field)
100|16|Birth DROID (object field)
116|4|FILETIME (high bytes) [2]
120|4|Unknown (0)

# Sector footer

Stored in the last 16 bytes of a log sector.

Offset (bytes)|Length (bytes)|Meaning
---|---|---
0|4|Lowest log entry index present
4|4|Next log entry index to be allocated when requested
8|8|Unused (0)

# Notes
1. domain-relative object ID,
2. thus, the timestamp resolution is almost 430 seconds.
