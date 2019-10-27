# Miscellaneous API
```rust
value_return(value_len: u64, value_ptr: u64)
```
Sets the blob of data as the return value of the contract.

###### Panics
* If `value_len + value_ptr` exceeds the memory container or points to an unused register it panics with `MemoryAccessViolation`;

---
```rust
panic()
```
Terminates the execution of the program with panic `GuestPanic("explicit guest panic")`.

---
```rust
panic_utf8(len: u64, ptr: u64)
```
Terminates the execution of the program with panic `GuestPanic(s)`, where `s` is the given UTF-8 encoded string.

###### Normal behavior
If `len == u64::MAX` then treats the string as null-terminated with character `'\0'`;

###### Panics
* If string extends outside the memory of the guest with `MemoryAccessViolation`;
* If string is not UTF-8 returns `BadUtf8`.
* If string length without null-termination symbol is larger than `config.max_log_len` returns `BadUtf8`.

---
```rust
log_utf8(len: u64, ptr: u64)
```
Logs the UTF-8 encoded string.

###### Normal behavior
If `len == u64::MAX` then treats the string as null-terminated with character `'\0'`;

###### Panics
* If string extends outside the memory of the guest with `MemoryAccessViolation`;
* If string is not UTF-8 returns `BadUtf8`.
* If string length without null-termination symbol is larger than `config.max_log_len` returns `BadUtf8`.

---
```rust
log_utf16(len: u64, ptr: u64)
```
Logs the UTF-16 encoded string. `len` is the number of bytes in the string.
See https://stackoverflow.com/a/5923961 that explains that null termination is not defined through encoding.

###### Normal behavior
If `len == u64::MAX` then treats the string as null-terminated with two-byte sequence of `0x00 0x00`.

###### Panics
* If string extends outside the memory of the guest with `MemoryAccessViolation`;

---
```rust
abort(msg_ptr: u32, filename_ptr: u32, line: u32, col: u32)
```
Special import kept for compatibility with AssemblyScript contracts. Not called by smart contracts directly, but instead
called by the code generated by AssemblyScript.


# Future Improvements

In the future we can have some of the registers to be on the guest.
For instance a guest can tell the host that it has some pre-allocated memory that it wants to be used for the register,
e.g.

```rust
set_guest_register(register_id: u64, register_ptr: u64, max_register_size: u64)
```
will assign `register_id` to a span of memory on the guest. Host then would also know the size of that buffer on guest
and can throw a panic if there is an attempted copying that exceeds the guest register size.