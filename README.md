# IRCIS (I Run Characters I See)

A grid based programming language with a visualizer inspired by [Befunge](https://en.wikipedia.org/wiki/Befunge).

The program works by reading the source file character by character and commands are executed based on the character read.

This reading is done by a *Runner* object which starts from the top left of the file and moves to the right.

Based on the character read, the *Runner* object can change the direction of parsing, create more *Runners*, store/print/calculate values and many more.
Check out the command list to see all the commands implemented.

The Visualizer helps in understanding the movement of these different *Runner* objects throughout the execution of the program.


## Character commands table

| Character | Commands                                                   |
|:---------:|:----------------------------------------------------------:|
| < > ^ v   | Movement control                                           |
| + - * / % | Arithmetic Ops in stack (in *Integer* mode)                |
| #         | Print stack top to output                                  |
| $         | Print Newline                                              |
| !         | End Processing                                             |
| <space> . | Blanks, Ignored                                            |
| "         | Toggles *Stack* mode                                       |
| '         | Goes to *Integer* mode                                     |
| @<n>      | Push n'th index element of stack to top. @0 duplicates top |
| &<n>      | Pop n elements from stack                                  |
| ?         | Check stack top. If true then continue, else go right/left |
| *         | Split into multiple Runners                                |
| @<str>    | Push value in variable <str> to stack                      |
| &<str>    | Set value in stack top to variable <str>                   |
|           |                                                            |


## Language Rules

### Basic and Movement
- Default/First Runner starts at first character of the source file((0, 0) position) and move right from there.
- The source file is transformed to a rectangular grid by padding blanks and processed as such.
- '.' (dot) and ' ' (space) characters are considered blanks and are ignored.
- Every Runner has its own stack.
- Runner parses and executes commands based on the character read in the position of the Runner.
- Movement of the Runner can be controlled using the characters '>', '^', 'v' and '>'.
- A Runner dies if it reaches the end of Grid, sees an End Processing(!) character or in the event of an error.

### Stack operations
- Stack push mode can be toggled with the double-quote(") character. In stack push mode, any character that comes in is pushed into the Runner stack as is.
- *Integer* mode can be used to push in integers and arithmetic operations. *Integer* mode processing starts on seeing a quote(') character.
- *Integer* mode has precedence over stack mode.
  i.e `"e'100.f` In this piece of code, 'e' is pushed to stack followed by integer 100 then f. The '.' (blank) after 100 is not pushed to stack but acts as a separator to signify end of *Integer* mode.
- *Integer* mode ends on seeing a blank character.
- Arithmetic operations are done when the operator is called in *Integer* mode.
- Arithmetic operations on the stack is done as:
  If A and B are the values on the stack, B is stack top and addition(+) is the operation called, then A and B are popped from stack and B+A is pushed back into the stack with operands in that order.
- The <n>'th element from the stack top can be pushed to the top of the stack by specifying <n> after the stack push '@' operator.
- Specifying <n> after the stack pop '&' operator, pops <n> elements from the stack.

### Printing
- Values can be written to output using hash(#) character.
- Dollar(\$) character starts a newline.

### Conditional
- Question mark(?) is used for conditional operation. It checks if the stack top value is non-zero.
- If the top value is non-zero, execution continues in the current direction.
- If the top value is zero, execution can go left or right depending on which side of the '?' there is a non-blank character. (Left and right here are with respect to the Runner object.)
- Left has higher priority if both sides have a non-blank character.

### Splitting/Creating more Runners
- Star(\*) character is used to create more Runner objects.
- On seeing a star, Runners are created in every direction to the star that has a non-blank character.
- The current Runner object continues in one of the split directions and new Runners are created if any, in other directions.
- The new Runners have a copy of the original Runner's stack.

### Variables
- Values can be saved to a variable by specifying a name <str> for the variable after the stack pop '&' operator. Saving to a variable doesn't pop the value from the stack.
- Values can be pushed to stack from a variable by specifying the variable name <str> after the stack push '@' operator.
- Variables names starting with a uppercase character are global variables and lowercase are local to the Runner instance.
- When a split happens the local variables are copied to both Runner instances.
- When 2 Runner instances try to write the same global variable in the same step, the value in the global variable depends on the Runner update order.

