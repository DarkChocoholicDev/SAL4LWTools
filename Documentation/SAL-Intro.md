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

## SAL at a Glance
In this section we'll take a brief look at the elements of structured assembly language as implemented by SAL4LWTools. There have been other SAL implementations over the years for a variety of microprocessors. This implementation was specifically influenced by C, C++, and C#.

Here are the key elements of SAL that we'll look at in this section.

- Operators
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

### Operators
SAL operators typically map to a single assembly language instruction. For instance, the ```+=``` operator maps to the ```add``` instruction, and the ```|=``` operator maps to the ```or``` instruction, such that:

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



**... WORK IN PROGRESS! ...**