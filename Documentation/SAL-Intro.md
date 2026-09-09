**... WORK IN PROGRESS! ...**


# An Introduction to SAL
Assembly language gives us full control of the hardware, letting us push beyond the limits of high level languages. But *standard* assembly language doesn't give us constructs like ```if-else``` statements and ```do-while``` loops to fully express the structure of our programs. Without them, much of our effort is spent on tediously implementing those constructs over and over until, ultimately, our beautifully-thought-out logic is lost in a sea of labels, comparisons, and jumps.

Consider the following routine that writes a string to the console, character by character, replacing control characters with spaces and converting lowercase characters to uppercase.

    ChrOut      equ $A002       ; Points to the CoCo ROM's character output routine.

    WriteFilteredString
        pshs    a,x

    WFS_Loop:
        lda     ,x+             ; Get the character at X, then increment X.
        beq     WFS_LoopEnd     ; Exit the loop if the character was $00.

        cmpa    #32             ; Is it a control character?
        bge     WSF_NotControl  ; Jump if not.
        lda     #' '            ; Else replace it with a space character.
        bra     WSF_NotLC       ; And go display it.
    WFS_NotControl:

        cmpa    #'a'            ; Is the character >= lowercase A?
        blo     WSF_NotLC       ; Jump if not.
        cmpa    #'z'            ; Is the character <= lowercase Z?
        bhi     WSF_NotLC       ; Jump if not.
        suba    #('a'-'A')      ; Convert the lowercase character to uppercase.
    WFS_NotLC:

        jsr     [ChrOut]        ; Write the character.
        bra     WSF_Loop        ; Loop back and process the next one.

    WFS_LoopEnd:
        puls    a,x
        rts

Although the logic itself is simple, it gets lost among the labels, comparisons, and jumps, making debugging and maintenance a chore. We need a more-effective way to express our assembly language logic.

## SAL: The Structured Alternative
*Structured* Assembly Language (SAL) combines the power and performance of assembly language with the readability and convenience of a C-like language, letting us use structured programming constructs to express our logic.

Here's the SAL version of our previous example. By using a few structured programming constructs (```if-else``` and ```while```), the program logic is now much more apparent.

    const void ChrOut() ptr = $A002;

    void WriteString() : preserve(a,x)
    {
        // While we haven't reached the end of the string...
        while (cc.notzero(a = [x++]))
        {
            // Adjust the character if necessary.
            if (a < 32)
            {
                // Replace control characters with single spaces.
                a = ' ';
            }
            else if (a >= 'a' && a <= 'z')
            {
                // Convert lowercase characters to uppercase.
                a -= ('a' - 'A');
            }

            // Display it.
            ChrOut();
        }
    }

When we code in SAL, we can focus on expressing our logic, letting the SAL translator worry about all the labels, comparisons, and jumps.

# SAL at a Glance
In this section we'll take a brief look at the elements of structured assembly language as implemented by SAL4LWTools. There have been other SAL implementations over the years for a variety of microprocessors. This implementation was specifically influenced by C, C++, and C#.

Here are the key elements of SAL that we'll look at in this section.

- [Operators](#operators)
- [Conditional expressions](#conditional-expressions)
- [Conditional execution and looping](#conditional-execution-and-looping)
- General expressions
- Statements and statement blocks
- [Variables and Constants](#variables-and-constants)
- Enumerations
- Structures
- Functions
- Namespaces
- Assembly directives

## Operators
SAL operators provide the means of expressing assembly language operations in a C-like manner.

### Introduction

SAL operators are patterned after C language *assignment* operators and typically map to a single assembly language instruction. For instance, the ```+=``` operator maps to the ```add``` instruction, while the ```|=``` operator maps to the ```or``` instruction, such that:

    a += 12;
    b |= $80;

yields:

    adda    #12
    orb     #$80

Some SAL operators map to a repetition of a single assembly language instruction. For instance, ```<<=``` operator maps to one or more instances of the ```lsl``` instruction, such that:

    b <<= 3;

yields:

    lslb
    lslb
    lslb

And finally, some 6809 operations that would benefit from having operators don't have a suitable equivalent in C-like languages, so SAL provides a way to write those operations in a way that keeps the feel of a C-like language. For instance, the ```MUL``` instruction, which multiplies the A and B registers and stores the result in the D register, can be written as ```d = a * b```; the traditional C-like multiplication assignment operator ```a *= b``` isn't used because it doesn't fully convey the behavior of the instruction.

### Operator Mapping
<table class="operator-table">
	<thead>
		<tr>
			<th>6809 Instruction</th>
			<th>SAL Operator</th>
			<th>Examples</th>
			<th>Remarks</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>ABX</td>
			<td>x += b</td>
			<td><code>x += b;</code></td>
			<td></td>
		</tr>
		<tr>
			<td>ADC</td>
			<td><em>r</em> += <em>m</em> + cc.carry</td>
			<td>
				<code>a += 10 + cc.carry;</code><br />
				<code>a += MyByte + cc.carry;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>ADD</td>
			<td>+=</td>
			<td>
				<code>a += 10;</code><br />
				<code>d += MyWord;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>AND</td>
			<td>&=</td>
			<td>
				<code>a &= 10;</code><br />
				<code>a &= MyByte;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>ANDCC</td>
			<td>cc.clear(<em>flags</em>)</td>
			<td>
				<code>cc.clear(cc.zero, cc.negative);</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>ASL</td>
			<td><<=</td>
			<td>
				<code>a <<= 3;</code><br/>
				<code>MyByte <<= 3;</code>
			</td>
			<td>ASL and LSL are the same opcode on the 6809.</td>
		</tr>
		<tr>
			<td>ASR</td>
			<td>>>=</td>
			<td>
				<code>signed(a) >>= 3;</code></br>
				<code>signed(MyByte) >>= 3;</code></br>
				<code>MyChar >>= 3;</code>
			</td>
			<td>ASR is used when the r/m value is signed;otherwise, LSR is used.</td>
		</tr>
		<tr>
			<td>BIT</td>
			<td>&</td>
			<td>
				<code>a & 10;</code><br />
				<code>a & MyByte;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>CLR</td>
			<td>-=</td>
			<td>
				<code>a -= a;</code><br />
				<code>MyByte -= MyByte;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>CMP</td>
			<td>==, !=, <, >, <=, >=</td>
			<td>
				<code>a == 10;</code><br />
				<code>x >= MyWord;</code>
			</td>
			<td>Used stand-alone or as part of a conditional expression.</td>
		</tr>
		<tr>
			<td>COM</td>
			<td>~=</td>
			<td>
				<code>a ~= a;</code><br />
				<code>MyByte ~= MyByte;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>CWAI</td>
			<td></td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>DAA</td>
			<td></td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>DEC</td>
			<td>--</td>
			<td>
				<code>a--;</code><br />
				<code>--a;</code><br />
				<code>MyByte--;</code><br />
				<code>--MyByte;</code>
			</td>
			<td>Currently, these always function as prefix operators.</td>
		</tr>
		<tr>
			<td>EOR</td>
			<td>^=</td>
			<td>
				<code>a ^= 10;</code><br />
				<code>a ^= MyByte;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>EXG</td>
			<td><-></td>
			<td>
				<code>a <-> b;</code><br />
				<code>a <-> dp;</code><br />
				<code>x <-> y;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>INC</td>
			<td>++</td>
			<td>
				<code>a++;</code><br />
				<code>++a;</code><br />
				<code>MyByte++;</code><br />
				<code>++MyByte;</code>
			</td>
			<td>Currently, these always function as prefix operators.</td>
		</tr>
		<tr>
			<td>LD</td>
			<td>=</td>
			<td>
				<code>a = 10;</code><br />
				<code>s = MyWord;</code><br />
				<code>a = [x++];</code><br />
				<code>d = [---s];</code><br />
				<code>a = x:MyStruct.MyByteField;</code><br />
				<code>a = x:MyStruct.MyWordField[1];</code>
			</td>
			<td>
				In memory references:
				<br/><span style="padding-left: 20px;" >++ post-increments by 1</span>
				<br/><span style="padding-left: 20px;" >+++ post-increments by 2</span>
				<br/><span style="padding-left: 20px;" >-- pre-decrements by 1</span>
				<br/><span style="padding-left: 20px;" >--- pre-decrements by 2</span>
			</td>
		</tr>
		<tr>
			<td>LEA</td>
			<td>--><br />--, ++, -=, +=</td>
			<td>
				<code>x --> pcr:MyByte;</code><br />
				<code>x --> x[a];</code><br />
				<code>x--;</code><br />
				<code>x++;</code><br />
				<code>x += 1000;</code><br />
				<code>x -= 1000;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>LSL</td>
			<td><<=</td>
			<td>
				<code>a <<= 3;</code><br />
				<code>MyByte <<= 3;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>LSR</td>
			<td>>>=</td>
			<td>
				<code>a >>= 3;</code><br />
				<code>MyByte >>= 3;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>MUL</td>
			<td>d = a * b</td>
			<td>
				<code>d = a * b;</code><br />
				<code>d = b * a;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>NEG</td>
			<td>-</td>
			<td>
				<code>a = -a;</code><br />
				<code>MyByte = -MyByte;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>OR</td>
			<td>|=</td>
			<td>
				<code>a |= 10;</code><br />
				<code>a |= MyByte;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>ORCC</td>
			<td>cc.set(<em>flags</em>)</td>
			<td>
				<code>cc.set(cc.zero, cc.negative);</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>PSHS</td>
			<td></td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>PSHU</td>
			<td></td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>PULS</td>
			<td></td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>PULU</td>
			<td></td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>ROL</td>
			<td><em>r</em>.rotcl(<em>n</em>)<br/>byte.rotcl(<em>m</em>, <em>n</em>)</td>
			<td>
				<code>a.rotcl(3);</code><br />
				<code>byte.rotcl(MyByte, 3);</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>ROR</td>
			<td><em>r</em>.rotcr(<em>n</em>)<br/>byte.rotcr(<em>m</em>, <em>n</em>)</td>
			<td>
				<code>a.rotcr(3);</code><br />
				<code>byte.rotcr(MyByte, 3);</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>SBC</td>
			<td><em>r</em> -= <em>m</em> + cc.carry</td>
			<td>
				<code>a -= 10 + cc.carry;</code><br />
				<code>a -= MyByte + cc.carry;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>SEX</td>
			<td></td>
			<td></td>
			<td></td>
		</tr>
		<tr>
			<td>ST</td>
			<td>=</td>
			<td>
				<code>MyByte = a;</code><br />
				<code>MyWord = s;</code><br />
				<code>[x++] = a;</code><br />
				<code>[---s] = d;</code><br />
				<code>x:MyStruct.MyByteField = a;</code><br />
				<code>x:MyStruct.MyWordField[1] = a;</code>
			</td>
			<td>
				In memory references:
				<br/><span style="padding-left: 20px;" >++ post-increments by 1</span>
				<br/><span style="padding-left: 20px;" >+++ post-increments by 2</span>
				<br/><span style="padding-left: 20px;" >-- pre-decrements by 1</span>
				<br/><span style="padding-left: 20px;" >--- pre-decrements by 2</span>
			</td>
		</tr>
		<tr>
			<td>SUB</td>
			<td>-=</td>
			<td>
				<code>a -= 10;</code><br />
				<code>d -= MyWord;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>TFR</td>
			<td>=</td>
			<td>
				<code>a = b;</code><br />
				<code>d = pc;</code>
			</td>
			<td></td>
		</tr>
		<tr>
			<td>TST</td>
			<td><em>r/m</em></td>
			<td>
				<code>a;</code><br />
				<code>MyByte;</code>
			</td>
			<td></td>
		</tr>
	</tbody>
</table>

## Conditional Expressions
Conditional expressions are used with the ```if``` statement and the various looping constructs. In the case of the ```if``` statement, the conditional expression is placed within the parentheses immediately following the ```if``` keyword.

    if (conditional-expression)
    {
        .
        .
        .
    }

The standard conditional operators are ```==```, ```!=```, ```<```, ```>```, ```<=```, and ```>=```. Conditional expressions can be combined using parentheses and the conditional operators ```||``` and ```&&```.

Examples:

    if (a == 13) ...

    if (a == 13 || a == 10) ...

    if (a != 0 && b >= 32) ...

    if ((a != 0 && b >= 32) || (a == 128 && b == 42) ...

The following condition code operators may also be used: ```cc.carry```, ```cc.nocarry```, ```cc.zero```, ```cc.notzero```, ```cc.overflow```, ```cc.nooverflow```, 
```cc.negative```, and ```cc.positive```. They can be used as-is within the conditional expression, or they can be used to evaluate the result of an expression, in the form of cc.*condition*(*expression*), such as ```cc.zero(a & $80)```.

| Condition Code Operator |
|----------|
| cc.carry |
| cc.nocarry |
| cc.zero |
| cc.notzero |
| cc.overflow |
| cc.nooverflow |
| cc.negative |
| cc.positive |

Examples:

    if (cc.zero) ...

    if (cc.notzero(b--)) ...

The following condition code operators may also be used instead of the standard conditional operators.

| Condition Code Operator | Meaning | Type | Standard Operator |
|---------------------|-------------|------|-----------------|
| cc.comp.eq | Equal | | == |
| cc.comp.ne | Not equal | | != |
| cc.comp.ge | Greater or equal | signed | >= |
| cc.comp.gt | Greater | signed | > |
| cc.comp.le | Less or equal | signed | <= |
| cc.comp.lt | Less | signed | < |
| cc.comp.hi | Greater or equal | unsigned | >= |
| cc.comp.hs | Greater | unsigned | > |
| cc.comp.ls | Less or equal | unsigned | <= |
| cc.comp.lo | Less | unsigned | < |

The following example shows how these operators might be used.

    //
    //  Write character (in A) to console, replacing control
    //  characters with spaces.
    //
    cmpa    #32;            // Is it a control character (< 32)?
    if (cc.comp_lt)         // If so...
    {
        lda     #32;        //     Replace it with a space character.
    }

    bsr     WriteChar;      // Display the character.



## Conditional Execution and Looping
SAL supports the ```if``` statement for simple conditional execution and the ```do-while```, ```for```, ```repeat-until```, and ```while``` statements for looping.

### The ```IF``` statement
Here's the syntax of the ```if``` statement. As with C, it can optionally have one or more ```else if``` clauses and an optional ```else``` clause.

    if (conditional-expression)
        statement
    else if (conditional-expression)
        statement
    .
    .
    .
    else
        statement

Example:

    if (b == 10 || b == 13)
    {
        b = 32;
    }
    else if (b > 126)
    {
        b = '.';
    }
    else
    {
        ToUpper();
    }

### The ```DO-WHILE``` loop
Unlike in the C language, the ```do-while``` loop has two forms. The first form is like that of C.

    do
        statement
    while (conditional-expression);

The second form includes a initializer.

    do (initializer)
        statement
    while (conditional-expression);

Examples:

```
do
{
    Console.ReadChar();
} while (a != 13);
```

```
do (a = ' ', b = 8)
{
    Console.WriteChar();
} while (cc.notzero(b--));
```

### The ```FOR``` loop
The ```for``` loop is much as it is in the C language, except that the initializer cannot declare local variables.

    for (initializer; conditional-expression; iterator)
        statement

Examples:

```
for (a = 'A'; a <= 'Z'; a++)
{
    Console.WriteChar();
}
```

```
for (a = 'A',b = 'Z'; a <= 'Z'; a++, b--)
{
    Console.WriteChar();
    a <-> b;
    Console.WriteChar();
    a <-> b;
}
```

### The ```REPEAT-UNTIL``` loop
Unlike the C language, SAL provides a ```repeat-until``` loop to complement the ```do-while``` loop, and it has two forms. The first is without an initializer.

    repeat
        statement
    until (conditional-expression_or_break)

The second form includes an initializer.

    repeat (initializer)
        statement
    until (conditional-expression_or_break)

In both forms, the conditional-expression can be the keyword ```break``` to loop indefinitely until the body of the loop executes a ```break``` statement.

Examples:

```
repeat
    Console.ReadChar();
until (a == 13);
```

```
repeat (b = 0)
    Console.ReadChar();
    if (a == Key.Break)
    {
        b = 1;
        break;
    }
    Console.WriteChar();
until (break);
```

### The ```WHILE``` loop
The ```while``` loop is much as it is with the C language.

    while (conditional-expression)
        statement

Example:

```
while (cc.notzero(a = [x++])
{
    Console.WriteChar();
}
```

## Variables and Constants

### Variables
Variables can be declared globally or locally (within a function), and they can be either a single instance or an array. When declared globally, they can have initializers.

***NOTE:** Structure variables and enumeration types cannot currently have initializers. I expect to add support for that in the future.*

The follow types are currently supported for variable declarations.

| Type | Sign/Unsigned | Width | Notes |
|------|---------------|-------|
| char | signed | 8 bits |
| byte | unsigned | 8 bits |
| int | signed | 16 bits |
| word | unsigned | 16 bits |
| enum | signed | 8 or 16 bits | Initialization not yet supported. |
| struct | n/a | user-defined | Initialization not yet supported. |

Examples:

    enum FurColor
    {
        Black = 0,
        Brown = 1,
        Golden = 2,
        White = 3,
        Spotted = 4
    }

    struct Pet
    {
        char Name[20];
        FurColor FurColor;
    }

    byte MyByte;
    byte MyInitializedByte = $99;
    char Key;
    char MsgHelloWorld[] = "Hello, World!";
    int MyInteger = 12345;
    word WordArray1[16];
    word WordArray2[] = { 100, 200, 300, 400, 500, 600, 700, 800 };
    Pet MyPet;
    char MyFurColor;

    MyPet.FurColor = a = FurColor.Brown;
    MyFurColor = a = FurColor.Golden;



**... WORK IN PROGRESS! ...**
