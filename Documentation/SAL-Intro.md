# An Introduction to SAL
Assembly language gives us the power and control to seemingly work miracles with the hardware, often going far beyond the limits of high level languages. But standard assembly language doesn't give us constructs like ```if-else``` statements and ```do-while``` loops to express the structure of our programs. Much of our code, then, is devoted to manually implementing the logic of those constructs, over...and over...and over again, flattened into a sea of comparisons, branches, and labels. No real structure; just a relatively straight line of code down the left side of the page.

In a simple routine with just a few comparisons, branches, and labels, the logic may not be too difficult to glean. For example, here's some code that loops through a string, writing characters to the console until a nul character is encountered. It's relatively easy to follow, especially when we use per-instruction comments to provide a running commentary.

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

But now let's amp up the complexity just a wee bit, changing the code so that it replaces control characters with spaces and converts lowercase characters to uppercase.

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

Although the logic itself is still very straightforward, "flattening" our structure like that makes it seem more complicated than it is. To follow the flow of the code, we must mentally reconstruct the structure that we just flattened.

There's got to be a better way, right? :wink: Right!

## SAL: The Structured Alternative
Structured Assembly Language (SAL) combines the power and performance of assembly language with the readability and convenience of high level languages, letting us express our low-level code using structured programming constructs. Here's the SAL version of our previous example. Notice how the logic is much easier to follow now that the structure is visible.

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

With *structured* assembly langauge, we can focus less on managing comparison, branches, and labels and more on implementing our program logic.