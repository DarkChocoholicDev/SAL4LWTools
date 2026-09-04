# An Introduction to SAL
Assembly language gives us full control of the hardware, letting us push beyond the limits of high level languages. But *standard* assembly language doesn't give us constructs like ```if-else``` statements and ```do-while``` loops to fully express the structure of our programs. Without them, much of our effort is spent on tediously implementing those constructs over and over, and ultimately, our beautifully-thought-out logic gets lost in a sea of labels, comparisons, and jumps.

In a very simple routine, the logic will likely be discernable despite the lack of structure. For instance, consider the code below which loops through each character of a string, writing characters to the console until a nul character is encountered. Even though the structure isn't apparent, the choice of label names along with the per-instruction comments helps to convey the logic.

    ChrOut      equ $A002       ; Points to the ROM's character output routine.
    
    WriteString:
        pshs    a,x             ; Preserve the A and X registers.
    Loop:
        lda	    ,x+             ; Get the next character to display, and advance the pointer.
        beq     Done            ; Leave the loop if it's a nul character ($00).
        jsr     [ChrOut]        ; Otherwise, write the character.
        bra     Loop            ; Loop back for the next one.
    Done:
        puls    a,x             ; Restore the A and X registers.
        rts

But let's increase the complexity a little, changing the code so that it replaces control characters with spaces and converts lowercase characters to uppercase.

    ChrOut      equ $A002       ; Points to the ROM's character output routine.

    WriteString
        pshs    a,x

    WS_Loop:
        lda     ,x+             ; Get the character at X, then increment X.
        beq     WS_LoopEnd      ; Exit the loop if the character was $00.

        cmpa    #32             ; Is it a control character?
        bge     WS_NotControl   ; Jump if not.
        lda     #' '            ; Else replace it with a space character.
        bra     WS_NotLC        ; And go display it.
    WS_NotControl:

        cmpa    #'a'            ; Is the character >= lowercase A?
        blo     WS_NotLC        ; Jump if not.
        cmpa    #'z'            ; Is the character <= lowercase Z?
        bhi     WS_NotLC        ; Jump if not.
        suba    #('a'-'A')      ; Convert the lowercase character to uppercase.
    WS_NotLC:

        jsr     [ChrOut]        ; Write the character.
        bra     WS_Loop         ; Loop back and process the next one.

    WS_LoopEnd:
        puls    a,x
        rts

In this case, gleaning the *intended* logic shouldn't take much time because of the self-documenting nature of the code, but what if we had to update it or debug a problem with it? For that, we'd need to figure out the *actual* logic, and the lack of structure greatly complicates that process.

There's got to be a better way, right? :wink: Right!

## SAL: The Structured Alternative
Structured Assembly Language (SAL) combines the power and performance of assembly language with the readability and convenience of C-like languages, letting us express our low-level code using structured programming constructs. Here's the SAL version of our previous example. Notice how the clear structure makes it much easier to discern the logic.

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

By writing in SAL, we can focus our effort on expressing our logic, leaving the sea of labels, comparisons, and jumps to the translator.

## What's in SAL?
In this section we'll survey the language features of SAL.