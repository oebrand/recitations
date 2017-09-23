# C Basics

C is a general-purpose programming language developed by Dennis Ritchie
at Bell Labs in the early 1970s.
It introduced a lot of the syntactic and programming conventions we see
in the numerous programming languages it influenced,
including Java, Javascript, Go, C#, Objective-C, and of course C++.

C code maps very efficiently to lower-level machine code,
giving programmers very precise control of the execution of their program.
Because of this, it's often used in systems programming
for low-latency, low-overhead, close-to-hardware applications.

Now, without further ado, let's take a look at C!


## Types

Before going into detail about the different types available in C,
it's important to convey what we mean by the term, "**type**"
(though without getting too technical with a mathematical definition).

For our purposes, and colloquially speaking, a type in C is just
how we choose to understand the data we find in memory.
It defines _how much data_ we should be expecting to read,
and _how to understand_ the data we find. 

Now, with that in mind, let's look at some types!

### Integer Types

Integers in C all use the binary number system we discussed previously;
two's complement for signed integers, and regular binary for unsigned.
So the only difference between integer types (of the same signedness)
is their size, which is also related to the range of values they can represent.

C only defines the size of each type in relation to the others:

    char <= short <= int <= long <= long long

Technically there's nothing stopping them from all of them being the same size,
but it's all dependent on what kind of machine and compiler you're using.
CLAC machines use the following:

Type        | Size
----------- | -------
`char`      | 1 byte
`short`     | 2 bytes
`int`       | 4 bytes
`long`      | 8 bytes
`long long` | 8 bytes

On most modern machines, the sizes for `char`, `short`, and `int`
are fairly consistent, and the same as what we have on CLAC.
However, the sizes for `long` and `long long` vary a lot from system to system.

You can always test it out for yourself:

```c
#include <stdio.h>
 
int main(void) {
    printf("char: %lu\n
            short: %lu\n
            int: %lu\n
            long: %lu\n
            longlong: %lu\n",
        sizeof(char), sizeof(short), sizeof(int),
        sizeof(long), sizeof(long long));
    return 0;
}
```

When we declare an integer type, we can modify it with the keyword
`signed` or `unsigned` to tell the compiler the signedness of the integer.
When unspecified, most integer types are signed by default.

C also allows the programmer to use `long` and `short` as modifiers for `int`,
which are the same as not having used the keyword `int` at all.
So don't be alarmed if you see a `long int` type! It's just a `long`.

Also, if we don't write `int` and only specify the signedness, it's an `int`.

The following are equivalent:

`short`                 | `short int`               | `signed short`        | `signed short int`
`long`                  | `long int`                | `signed long`         | `signed long int`
`long long`             | `long long int`           | `signed long long`    | `signed long long int`
`int`                   | `signed`                  | `signed int`
`unsigned int`          | `unsigned`
`unsigned short`        | `unsigned short int`
`unsigned long`         | `unsigned long int`
`unsigned long long`    | `unsigned long long int`

This is just a detail to watch out for, but not super important.
Most of the time we use the notation in the leftmost column, by convention.

### Floating-Point Types

There are three types for floating-point numbers in C,
for single-precision, double-precision, and extended-precision floating point.
How they're bitwise represented differs from machine to machine,
but most modern computers use the IEEE-754 floating-point standard.
If you're curious to see what it looks like,
you can reuse some of your code from lab1 to do so
(don't worry about the casting stuff just yet, it'll make sense later on):

```c
float f = 3.157;        // or whatever you want it to be!
int *i = (int *) &f;    // don't worry about what this means
                        // we're tricking the compiler to think that
                        // the float is a binary (;

print_hex(*i);
print_binary(*i);       // implementation is left as an exercise to the reader
```

Like with integers, the sizes are defined relative to each other.
They are:

    float <= double <= long double

The sizes on CLAC are as follows:

Type            | Size
--------------- | ---------
`float`         | 4 bytes
`double`        | 8 bytes
`long double`   | 16 byetes

Again, you can test it out for yourself:

```c
#include <stdio.h>

int main(void) {
    printf("float: %lu\n
            double: %lu\n
            long double: %lu\n",
        sizeof(float), sizeof(double), sizeof(long double));
    return 0;
}
```



Here are some declarations to help you understand what really happens when we're talking characters and integers. Definitely take a look at 
[The Ascii Table](http://www.asciitable.com) and understand the relationships 
in theorder of the first 128 ascii characters. The C language is built on a 
subset of 7-bit ascii (0-127) so its important to know what the table is like, 
not to memorize it. Also note that in C, single quotes means a character.

Declaration     | x (dec) | y (dec)
--------------- | ------- | -------
int x;          | NULL    | -
int x, y;       | NULL    | NULL
int x = 0, y;   | 0       | NULL
char x = 'x';   | 120     | -
char x = '\n';  | 10      | -
char x = '\13'; | 11      | -
char x = '0';   | 48      | -
char x = '\0';  | 0       | -
char x = 0;     | 0       | -
long x = 0L;    | 0       | -

Preceding a constant with 0x denotes hexadecimal notation:

    (0xFFFFFFFF == -1); //returns 1 (which is true, but C doesn't have true)
    (037777777777 == -1); //returns 1 (true)
    sizeof(1234L); //returns 8
    sizeof(1234); //returns 4
    0xFFFFU; //returns 65535
    0177777U; //returns 65535

Also not that converting a signed value to an unsigned value or vice verse
preserves the bit pattern:

```c
  char c = -1; //0xFF
  unsigned char uc = c; //0xFF
  int i = uc; //i == 255
```


In C there is no such thing as a string, just an array and pointers.
Essentially, a bunch of single characters located consecutively in memory will
make up a string, but more on this later.


## Expressions ##

    char *myString = "Here's a string!" //string literal
    int x = 10; //variable declaration and assignment
    int x; //variable declaration
    x = 30; //variable assignment: lvalue = rvalue

The order of the increment/decrement operators in C matters:

    int x = 1;
    int y = x++; // y==1
    y = ++x; // y==3

Valid binary operators in order of increasing precedence:

  - + -
  - * / %

The positive and negative operators are more tightly binding than any of the
above operators (+/-).

Comparison operators:

  - < > <= >=

All of these comparison operators have the same precedence, and are more tightly
binding than the equality and inequality operators (==/!=).

The logical operators are `||` for or, `&&` for and, and `!` for not. The "or"
and "and" operators short-circuit.

[Bitwise operators](recitation-2.md#bitwise-operators) are tricky and can be 
used for a variety of purposes. See the examples in the recitation 2 notes for 
a refresher.

Ternary operator:

```c
x = a ? b : c;
```

is the same thing as this:

```c
if(a)
  x = b;
else
  x = c;
```

Note that any integer is also a boolean!! 0 is false, any other number is true!


## Statements ##

### If-Else Statements ###

```c
if(condition)
  if(condition2) {
    printf("conditions met");
    return 0;
  }
  else
    printf("no conditions met");
```

### Switch Statements ###

```c
switch(v) {
  case 1: // v == 1
    printf("v is 1");
    break;
  case 2:
    printf("v is 2");
    break;
  default:
    printf("v is neither 1 nor 2");
}
```

### Loops ###

```c
int i;
for(i = 0; i < 10; i++) {
    // do things here
}
while(i) { //condition checked at the beginning
    //other things
    i--;
}

do {
    //more things
} while(i < 5); //condition is checked at the end
```

Using `break;` inside a loop will break out of the innermost loop. Using
`continue;` will stop executing the current iteration of the loop and skip to
the next iteration. `for(;;)` is an idiom for an infinite loop. `goto label`
will jump to a line beginning with `label: `. Be careful with gotos.
