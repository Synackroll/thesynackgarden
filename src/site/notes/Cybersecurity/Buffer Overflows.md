---
{"dg-publish":true,"permalink":"/cybersecurity/buffer-overflows/"}
---


Occur when there is a buffer of characters, integers, or any other type of variables and someone inserts into this buffer more bytes than it can store.

When this happens tje extra bytes are stored in memory after the address of the buffer, overwriting important addresses for the program flow which usually causes a crash.

Whenever a function is called, the program knows where to return to because the `return address` is stored. If we can overwrite the address, we can redirect the flow of the program.

in gdb, we can run `p <function_name>` to print the function's address.

To perform a buffer overflow we consider:

1. Canary is disabled so it won't quit after the canary address is overwritten.                      
2. PIE is disabled so the addresses of the binary functions are not randomized and the user knows  where to return after overwritting the return address.
3. There is a buffer with N size.
4. There is a function that reads to this buffer more than N bytes.

`Run printf 'A%.0s' {1..30} | ./test to enter 30*"A" into the program.`

HTB{th30ry_bef0r3_4cti0n}