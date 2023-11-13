- Pulling out data from strings.
	- Specify keywords, whitespaces, identifiers, numbers, operations, comments.
	- Use this to split the string into tokens, and be able to parse
	- "if true then then 42 else +"
	- KEYWORD(if),
	  WHITESPACE,
	  IDENT(true),
	  WHITESPACE,
	  KEYWORD(then),
	  WHITESPACE,
	  KEYWORD(then),
	  WHITESPACE,
	  NUM(42),
	  WHITESPACE,
	  KEYWORD(else),
	  WHITESPACE,
	  OP(+)
	- remove whitespace and comments
	- KEYWORD(if),
	  IDENT(true),
	  KEYWORD(then),
	  KEYWORD(then),
	  NUM(42),
	  KEYWORD(else),
	  OP(+)
- POSIX: Two Rules
	- Longest match rule (“maximal munch rule”): The longest initial substring matched by any regular expression is taken as the next token.
	- Rule priority: For a particular longest initial substring, the first regular expression that can match determines the token.
- Regexes and Values
	- Regular expressions and their corresponding values:
		- r ::= 0
		  | 1
		  | c
		  | r1 · r2
		  | r1 + r2
		  
		  | r∗
		- v ::=
		  Empty
		  | Char(c)
		  | Seq(v1, v2)
		  | Left(v)
		  | Right(v)
		  | Stars []
		  | Stars [v1, . . . vn]
- ![image.png](../assets/image_1699892266437_0.png)
-