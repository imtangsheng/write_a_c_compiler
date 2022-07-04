
## Week 1: Integers

Here’s a program we’d like to compile - we’ll call it return_2.c:
```
int main() {
    return 2;
}
```

need to compile a program, run it, and check its return code:
```
$ ./YOUR_COMPILER return_2.c # compile the source file shown above
$ ./gcc -m32 return_2.s -o return_2 # assemble it into an executable
$ ./return_2 # run the executable you just compiled
$ echo $? # check the return code; it should be 2
```

To see how this program looks in assembly, let’s compile it with gcc3:
```
$ gcc -S -O3 -fno-asynchronous-unwind-tables return_2.c
$ cat return_2.s
    .section __TEXT,__text_startup,regular,pure_instructions
    .align 4
    .globl _main
_main:
    movl    $2, %eax
    ret
    .subsections_via_symbols
```

Finally, we have our actual assembly instructions:
```
_main:                  ; label for start of "main" function
    movl    $2, %eax    ; move constant "2" into the EAX register
    ret                 ; return from function
```
Here’s a 20-line Python script to do that:
```
import sys, os, re

#expected form of a C program, without line breaks
source_re = r"int main\s*\(\s*\)\s*{\s*return\s+(?P<ret>[0-9]+)\s*;\s*}" 

# Use 'main' instead of '_main' if not on OS X
assembly_format = """    
    .globl _main
_main:
    movl    ${}, %eax
    ret
"""

source_file = sys.argv[1]
assembly_file = os.path.splitext(source_file)[0] + ".s"

with open(source_file, 'r') as infile, open(assembly_file, 'w') as outfile:
    source = infile.read().strip()
    match = re.match(source_re, source)

    # extract the named "ret" group, containing the return value
    retval = match.group('ret') 
    outfile.write(assembly_format.format(retval))
```
 
 We’ll define it here in Backus-Naur Form:

<program> ::= <function>
<function> ::= "int" <id> "(" ")" "{" <statement> "}"
<statement> ::= "return" <exp> ";"
<exp> ::= <int>

Here’s the pseudocode for parsing a statement:
```
def parse_statement(tokens):
    tok = tokens.next()
    if tok.type != "RETURN_KEYWORD":
        fail()
    tok = tokens.next()
    if tok.type != "INT"
        fail()
    exp = parse_exp(tokens) //parse_exp will pop off more tokens
    statement = Return(exp)

    tok = tokens.next()
    if tok.type != "SEMICOLON":
        fail()

    return statement
```

Here’s the assembly we need:

To generate a function (e.g. function “foo”):
```
 .globl _foo
_foo:
 <FUNCTION BODY GOES HERE>
To generate a return statement (e.g. return 3;):
 movl    $3, %eax
 ret
```
Here’s what nqcc’s pretty printer outputs for return_2.c:
```
FUN INT main:
    params: ()
    body:
        RETURN Int<2>
```