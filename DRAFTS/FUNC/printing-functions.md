# DRAFT: Extended Printing & Debug Functions
## Status
Draft ☑ Experimental ☐ Proposal ☐ Accepted ☐ Rejected ☐

## Summary
Extend the language's output functions with a set of functions to facilitate debugging, introspection, and presentation of data in various formats.

## Functions Overview
### `print(expr)`
Prints the value without the end of line (`n`).

```ns
print("Hello, ")
print("World!")  # → Hello, World!
```

### `println(expr)`
Print the value with the end of line.
```ns
println("Hello")  # → Hello\n
```

### `print_debug(x)`
Print the type and value in raw form.
```ns
print_debug("abc")  # → String("abc")\n
print_debug(42)     # → Int(42)\n
```

### `print_typeof(x)`
Returns the name of the variable type.
```ns
print_typeof(42)  # → Int
print_typeof(true)  # → Bool
```

### `print_bin(x)`
Returns the binary representation of a number (with prefix 0b).
```ns
print_bin(5)  # → 0b101
print_bin(255)  # → 0b11111111
```

### `print_json(x)`
Converts the data structure into JSON-formatted text and prints out.
```ns
print_json({"a": 1})  # → {"a":1}
print_json([1, 2, 3]) # → [1,2,3]
```

### `print_stack()`
Print the current stack of function calls (backtrace).
```ns
print_stack()
# → main() -> foo() -> bar()
```

### `eprint(x)`
It writes x on STDERR instead of STDOUT.
```ns
eprint("error occurred")  # (on stderr)
```

### `dump_state()`
Print all current variables and their values (for debugging).
```ns
at status:
# x = 1, y = 2, z = "ok"

dump_state()
# → x=1, y=2, z="ok"
```