# Whimsy Grammar

This is the complete grammar for Whimsy. A program consists of zero or more statements, followed by the end of the file. The format is a combination of EBNF and regular expressions, with `A?` indicating an optional `A`, `A*` is zero or more `A`s, and `A+` is one or more `A`s.

## Statements
```
      statement -> const_decl | var_decl | assignment
                 | block_stmt
                 | expr_stmt
                 | for_stmt
                 | if_stmt
                 | loop_stmt
                 | return_stmt
                 | empty_stmt

     const_decl -> (expression ".")? identifier "::" expression
       var_decl -> (expression ".")? identifier ":=" expression
     assignment -> (expression ".")? identifier ("=" | "+=" | "-=" | "*=" | "/=" | "%=") expression

loop_statements -> statement | break_stmt | continue_stmt

     block_stmt -> "do" statement* "/do"
     break_stmt -> "break" expression
  continue_stmt -> "continue" expression
      expr_stmt -> expression
       for_stmt -> "for" identifier "in" expression loop_statements* "/for"
        if_stmt -> "if" expression statement*
                   ("elif" expression statement*)*
                   ("else" statement*)
                   "/if"
      loop_stmt -> "loop" loop_statements* "/loop"
    return_stmt -> "return" expression

     empty_stmt -> ";"
```

## Expressions
```
     expression -> logic_or

       logic_or -> logic_and ("or" logic_and)*
      logic_and -> equality ("and" equality)*
       equality -> comparison (("==" | "!=") comparison)*
     comparison -> range (("<" | "<=" | ">" | ">=" | "is") range)*
          range -> term ((".." | "..=") term ("by" expression)?)*
           term -> factor (("+" | "-") factor)*
         factor -> custom (("*" | "/" | "%") custom)*
         custom -> unary (custom_op unary)*

          unary -> ("!" | "-") unary | call
           call -> primary (
                               "(" arguments? ")"
                             | "." (identifier | string)
                             | ":" (identifier | string) "(" arguments? ")"
                             | "[" expression "]"
                           )*
        primary -> "true" | "false" | "nil"
                 | number | string | identifier
                 | "(" expression ")"
                 | if_expr
                 | class | function
                 | list | map | set

        if_expr -> "if" expression ";"? expression
                   ("elif" expression ";"? expression)*
                   "else" expression

          class -> "class" ("is" expression)? statement* "/class"
       function -> "fn" "(" parameters? ")" statement* "/fn"
           list -> "(" ((expression ",") | (expression ("," expression)+ ","?))? ")"
            map -> "[" (expression ("::" | ":=") expression ("," expression ("::" | ":=") expression)* ","?)? "]"
            set -> "[" expression ("," expression)* ","? "]"
```

## Helpers
```
     parameters -> identifier ("," identifier)* ","?
      arguments -> expression ("," expression)* ","?
```

## Lexical Grammar
```
          alpha -> "a"..."z" | "A"..."Z" | "_"
         binary -> "0" | "1" | "_"
          octal -> "0"..."7" | "_"
        decimal -> "0"..."9" | "_"
    hexadecimal -> "0"..."9" | "a"..."f" | "A"..."F" | "_"

      custom_op -> (
                       "~" | "!" | "@" | "$" | "%" | "^" | "&" | "*" | "-" | "+" | "="
                     | "{" | "}" | "\" | "|" | ";" | ":" | "<" | ">" | "." | "?" | "/"
                   )+
                 | "`" identifier

         number -> ("0b" | "0B") binary+ ("." binary+)?
                 | ("0o" | "0O") octal+ ("." octal+)?
                 | decimal+ ("." decimal+)?
                 | ("0x" | "0X") hexadecimal+ ("." hexadecimal+)?
         string -> '"' (<not '"'> | '\"')* '"'
                 | "'" (<not "'"> | "\'")* "'"
     identifier -> alpha (alpha | decimal)*
```
