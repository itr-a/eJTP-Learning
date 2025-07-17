# What is ADS?

**ADS** = **Alternate Data System**\
ADS is a way to hide extra data inside a normal-lookin file on NTFS (Windows) file systems.
> A secret drawer hidden behind a regular-ooking cabinet

### Limitations
Only works on NTFS
Many tools ignore ADS, so it's sneaky\

### Example
Normal cat.txt file
```bash
cat.txt
```
**Hide something inside it** like this:
```bash
echo HACKED > cat.txt:hidden.txt
```
Now there are *two parts* in that one fle:
- `cat.txt` -> the normal file I can see
- `hidden.txt` -> a secret stream inside it
Even if someone opens `cat.txt`, they won't see the hidden part