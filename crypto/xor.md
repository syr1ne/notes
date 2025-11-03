```python
print("".join(chr(o ^ 0x32) for o in ords))
```

in here, o ^ 0x32 (or 50) performs XOR on o.
note: dont confuse it with "power of". in python, we do power of using "^^" this

## what is XOR?

XOR is a bitwise operation. for example: 

XOR of 81 and 50 is:
```
81 (digit) == 1010001 (binary)
50 (digit) == 0110010 (binary)
-------------------------------
99 (digit) ==   1100011 (binary) -----------> XOR OPERATION
```

then we are sending output (99) to chr() function which evaluate hex (0x99) to ascii character, which becomes 'c'

