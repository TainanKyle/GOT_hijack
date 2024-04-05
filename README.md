# GOT Maze Challenge
Hijack some functions using the GOT table to solve the maze.

### Description
1. The main program (maze.c) registers the address of its main function, initializes the library, and loads an existing maze from /maze.txt. 
It then calls move_NNN functions in a fixed order to perform random walks in the maze. Obviously, the random walk process cannot solve the maze.
2. The move_NNN functions are all implemented in the shared library, I hijacked the function calls from the main function to the move_NNN functions by modifying the GOT table of the corresponding functions.
3. Retrieved the relative address of the main function and each GOT table entry from the binary by pwntools using the script.
```
from pwn import *
elf = ELF('./maze')
print("main =", hex(elf.symbols['main']))
print("{:<12s} {:<10s} {:<10s}".format("Func", "GOT Offset", "Symbol Offset"))
for s in [ f"move_{i}" for i in range(1200)]:
   if s in elf.got:
      print("{:<12s} {:<10x} {:<10x}".format(s, elf.got[s], elf.symbols[s]))
```
4. After compiled as a shared object `remote_solver.so` file, run `LD_PRELOAD=./remote_solver.so ./maze` to solve the maze.
