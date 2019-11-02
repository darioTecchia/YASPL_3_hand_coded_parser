# YASPL

Programming language definited for the Compiler course from University.

### Project Part 3
Hand Coded Parser

## Regular Definition

|Regular Definition | Regular Expression|
|-------------------|-------------------|
|LineTerminator | `\r\|\n\|\r\n` |
|InputCharacter | `[^\r\n]` |
|WhiteSpace | `{LineTerminator} \| [ \t\f]` |
|||
|Comment | `{TraditionalComment} \| {EndOfLineComment} \| {DocumentationComment}` |
|TraditionalComment | `"/*" [^*] ~"*/" \| "/*" "*"+ "/"` |
|EndOfLineComment | `"//" {InputCharacter}* {LineTerminator}?` |
|DocumentationComment | `"/**" {CommentContent} "*"+ "/"` |
|CommentContent | `( [^*] \| \*+ [^/*] )*` |
|||
|Identifier | `[:jletter:] [:jletterdigit:]*` |
|||
|IntegerLiteral | `0 \| [1-9][0-9]*` |
|||
|FloatLiteral | `(0 \| [1-9][0-9]*)\.[0-9]+` |

## Lexical Specification

|Token |Lexeme or Regular Definition|Attribute|
|------|------|---------|
|**Delimiters**|
|*-*|WhiteSpace|-|
||||
|**Separators**|
|LPAR|(|-|
|RPAR|)|-|
|LBRA|{|-|
|RBRA|}|-|
|COMMA|,|-|
|SEMI|;|-|
||||
|**Keywords**|
|IF|if|-|
|THEN|then|-|
|ELSE|else|-|
|WHILE|while|-|
||||
|**Identifiers**|
|ID|Identifier|Pointer to table entry|
||||
|**Numbers**|
|INT|IntegerLiteral|Pointer to table entry|
|FLOAT|FloatLiteral|Pointer to table entry|
||||
|**Relops**|
|<|<|LT|
|<=|<=|LE|
|=|=|EQ|
|<>|<>|NE|
|>|>|GT|
|>=|>=|GE|
|<--|<--|ASSIGN|

## Grammar Specification

Tokens are mostly those defined in exercise 1.

G = ( \
&ensp; N = {Program, Stmt, Expr, Term}, \
&ensp; T = {ID, IF, THEN, ELSE, RELOP, NUMBER, SEMI, ASSIGN, WHILE, DO}, \
&ensp; S = Program \
&ensp; P =

```
  Program -> Program ; Stmt
  Program -> Stmt
  Stmt -> IF Expr THEN Stmt ELSE Stmt
  Stmt -> ID ASSIGN Expr
  Stmt -> WHILE Expr DO Stmt
  Expr -> Term RELOP Term
  Expr -> Term
  Term -> ID
  Term -> NUMBER
```
)

The initial grammar is not suitable for top down parsing, removing recursion:

```
  Program -> Stmt FProgram
  FProgram -> ; Stmt FProgram
  FProgram -> eps
  Stmt -> IF Expr THEN Stmt ELSE Stmt
  Stmt -> ID ASSIGN Expr
  Stmt -> WHILE Expr DO Stmt
  Expr -> Term RELOP Term
  Expr -> Term
  Term -> ID
  Term -> NUMBER
```

Removing factorization:

```
  Program -> Stmt FProgram
  FProgram -> ; Stmt FProgram
  FProgram -> eps
  Stmt -> IF Expr THEN Stmt ELSE Stmt
  Stmt -> ID ASSIGN Expr
  Stmt -> WHILE Expr DO Stmt
  Expr -> Term FExpr
  FExpr -> relop Term
  FExpr -> eps
  Term -> ID
  Term -> NUMBER
```

Using the increased production G for better EOF handling:
```
  G -> Program EOF
  Program -> Stmt FProgram
  FProgram -> ; Stmt FProgram
  FProgram -> eps
  Stmt -> IF Expr THEN Stmt ELSE Stmt
  Stmt -> ID ASSIGN Expr
  Stmt -> WHILE Expr DO Stmt
  Expr -> Term FExpr
  FExpr -> relop Term
  FExpr -> eps
  Term -> ID
  Term -> NUMBER
```

The final implemented grammar are the following:
```
  G -> Program EOF
  Program -> Stmt FProgram
  FProgram -> SEMI Stmt FProgram
  FProgram -> eps
  Stmt -> IF Expr THEN Stmt ELSE Stmt
  Stmt -> ID ASSIGN Expr
  Stmt -> WHILE Expr DO Stmt
  Expr -> Term FExpr
  FExpr -> RELOP Term
  FExpr -> eps
  Term -> ID
  Term -> INT
  Term -> FLOAT
```



## Notes
 - The requested *sym* class is named Token;
 - The grammar's production have been increased for a better *EOF* handling;
 - *RELOP* values are explained in the *Lexical Specification* table, range 7-13 in     `Token.TOKEN` class;
 - ';' is *SEMI*.
