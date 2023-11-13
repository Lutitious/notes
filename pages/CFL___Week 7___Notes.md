- Test Program:
	- ```scala
	  start := 1000;
	  x := start;
	  y := start;
	  z := start;
	  while 0 < x do {
	  	while 0 < y do {
	  		while 0 < z do { z := z ‐ 1 };
	  		z := start;
	  		y := y ‐ 1
	  	};
	  	y := start;
	  	x := x ‐ 1
	  }
	  ```
- Compiling AExps, For example 1 + ((2 * 3) + (4 - 3)):
	- ```jvm
	  ldc 1
	  ldc 2
	  ldc 3
	  imul
	  ldc 4
	  ldc 3
	  isub
	  iadd
	  iadd
	  ```
-
- Compiling AExps
	- id:: 65527ac7-c6f4-4a4f-8bd2-43e390da7e56
	  $$compile(n, E) \stackrel{def}{=} ldc\ n$$
	- $$compile(a_1 + a_2,E) \stackrel{def}{=}compile(a_1,E) @ compile(a_2,E) @ iadd$$
	- $$compile(a_1 − a_2,E) \stackrel{def}{=}compile(a_1,E) @ compile(a_2,E) @ isub$$
	- $$compile(a_1 * a_2,E) \stackrel{def}{=}compile(a_1,E) @ compile(a_2,E) @ imul$$
	- $$compile(x, E) \stackrel{def}{=} iload\ E(x)$$
- Variables
	- x := 5 + y ∗ 2
	- lookup: `iload` index
	- store: `istore` index
	- while compiling we have to maintain a map between our identifiers and the Java bytecode indices
-
- Compilation of some mathematical functions:
	- Aop("+", a1, a2) ⇒ ...iadd
	- Aop("‐", a1, a2) ⇒ ...isub
	- Aop("*", a1, a2) ⇒ ...imul
	- Aop("/", a1, a2) ⇒ ...idiv
	- Aop("%", a1, a2) ⇒ ...irem
-
- Compiling Statements
	- We return a list of instructions and an environment
	  for the variables
	- $$compile(skip, E) \stackrel{def}{=} (Nil, E)$$
	  $$compile(x := a, E) \stackrel{def}{=} (compile(a, E) @ istore index, E(x 7 → index))$$
	- where index is E(x) if it is already defined, or if it is not, then the largest index not yet seen
-
- Compiling Assignments, x := x + 1
	- ```jvm
	  iload nx
	  ldc 1
	  iadd
	  istore nx
	  ```
	- where nx is the index corresponding to the variable x
-