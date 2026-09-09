==**... WORK IN PROGRESS! ...**==


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

- [Operators](#Operators)
- Conditional expressions
- Conditional execution and looping
- General expressions
- Statements and statement blocks
- Variables
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
<style>
    .operator-table td:nth-child(2) {
		text-align: center;
		/*font-family: monospace;*/
	}

    .operator-table td:nth-child(3) { 
		font-family: monospace;
	}
</style>
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
			<td>x += b;</td>
			<td></td>
		</tr>
		<tr>
			<td>ADC</td>
			<td><em>r</em> += <em>m</em> + cc.carry</td>
			<td>a += 10 + cc.carry;<br />a += MyByte + cc.carry;</td>
			<td></td>
		</tr>
		<tr>
			<td>ADD</td>
			<td>+=</td>
			<td>a += 10;<br />d += MyWord;</td>
			<td></td>
		</tr>
		<tr>
			<td>AND</td>
			<td>&=</td>
			<td>a &= 10;<br />a &= MyByte;</td>
			<td></td>
		</tr>
		<tr>
			<td>ANDCC</td>
			<td>cc.clear(<em>flags</em>)</td>
			<td>cc.clear(cc.zero, cc.negative);</td>
			<td></td>
		</tr>
		<tr>
			<td>ASL</td>
			<td><<=</td>
			<td>a <<= 3;<br/>MyByte <<= 3;</td>
			<td>ASL and LSL are the same opcode on the 6809.</td>
		</tr>
		<tr>
			<td>ASR</td>
			<td>>>=</td>
			<td>signed(a) >>= 3;</br>signed(MyByte) >>= 3;</br>MyChar >>= 3;</td>
			<td>ASR is used when the r/m value is signed;otherwise, LSR is used.</td>
		</tr>
		<tr>
			<td>BIT</td>
			<td>&</td>
			<td>a & 10;<br />a & MyByte;</td>
			<td></td>
		</tr>
		<tr>
			<td>CLR</td>
			<td>-=</td>
			<td>a -= a;<br />MyByte -= MyByte;</td>
			<td></td>
		</tr>
		<tr>
			<td>CMP</td>
			<td>==, !=, <, >, <=, >=</td>
			<td>a == 10;<br />x >= MyWord;</td>
			<td>Used stand-alone or as part of a conditional expression.</td>
		</tr>
		<tr>
			<td>COM</td>
			<td>~=</td>
			<td>a ~= a;<br />MyByte ~= MyByte;</td>
			<td></td>
		</tr>
		<tr>
			<td>CWAI</td>
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
			<td>a--;<br />--a;<br />MyByte--;<br />--MyByte;</td>
			<td>Currently, these always function as prefix operators.</td>
		</tr>
		<tr>
			<td>EOR</td>
			<td>^=</td>
			<td>a ^= 10;<br />a ^= MyByte;</td>
			<td></td>
		</tr>
		<tr>
			<td>EXG</td>
			<td><-></td>
			<td>a <-> b;<br />a <-> dp;<br />x <-> y;</td>
			<td></td>
		</tr>
		<tr>
			<td>INC</td>
			<td>++</td>
			<td>a++;<br />++a;<br />MyByte++;<br />++MyByte;</td>
			<td>Currently, these always function as prefix operators.</td>
		</tr>
		<tr>
			<td>LD</td>
			<td>=</td>
			<td>a = 10;<br />s = MyWord;<br />a = [x++];<br />d = [---s];<br />a = x:MyStruct.MyByteField;<br />a = x:MyStruct.MyWordField[1];</td>
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
			<td>x --> pcr:MyByte;<br />x --> x[a];<br />x--;<br />x++;<br />x += 1000;<br />x -= 1000;</td>
			<td></td>
		</tr>
		<tr>
			<td>LSL</td>
			<td><<=</td>
			<td>a <<= 3;<br />MyByte <<= 3;</td>
			<td></td>
		</tr>
		<tr>
			<td>LSR</td>
			<td>>>=</td>
			<td>a >>= 3;<br />MyByte >>= 3;</td>
			<td></td>
		</tr>
		<tr>
			<td>MUL</td>
			<td>d = a * b</td>
			<td>d = a * b;<br />d = b * a;</td>
			<td></td>
		</tr>
		<tr>
			<td>NEG</td>
			<td>-</td>
			<td>a = -a;<br />MyByte = -MyByte;</td>
			<td></td>
		</tr>
		<tr>
			<td>OR</td>
			<td>|=</td>
			<td>a |= 10;<br />a |= MyByte;</td>
			<td></td>
		</tr>
		<tr>
			<td>ORCC</td>
			<td>cc.set(<em>flags</em>)</td>
			<td>cc.set(cc.zero, cc.negative);</td>
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
			<td>a.rotcl(3);<br />byte.rotcl(MyByte, 3);</td>
			<td></td>
		</tr>
		<tr>
			<td>ROR</td>
			<td><em>r</em>.rotcr(<em>n</em>)<br/>byte.rotcr(<em>m</em>, <em>n</em>)</td>
			<td>a.rotcr(3);<br />byte.rotcr(MyByte, 3);</td>
			<td></td>
		</tr>
		<tr>
			<td>SBC</td>
			<td><em>r</em> -= <em>m</em> + cc.carry</td>
			<td>a -= 10 + cc.carry;<br />a -= MyByte + cc.carry;</td>
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
				MyByte = a;<br />
				MyWord = s;<br />
				[x++] = a;<br />
				[---s] = d;<br />
				x:MyStruct.MyByteField = a;<br />
				x:MyStruct.MyWordField[1] = a;
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
			<td>a -= 10;<br />d -= MyWord;</td>
			<td></td>
		</tr>
		<tr>
			<td>TFR</td>
			<td>=</td>
			<td>a = b;<br />d = pc;</td>
			<td></td>
		</tr>
		<tr>
			<td>TST</td>
			<td><em>r/m</em></td>
			<td>a;<br />MyByte;</td>
			<td></td>
		</tr>
	</tbody>
</table>

==**... WORK IN PROGRESS! ...**==