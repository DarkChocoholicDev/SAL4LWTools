# SAL4LWTools
6809/6309 Structured Assembly Language translator, targeting the LWTools assembler and linker.

This tool takes structured assembly language source files (for the 6809/6309 microprocessor) and translates them for use as input to
the LWTools assembler/linker tool chain. The SAL source files consist of assembly language instructions written in a C-language-like
manner, combining the readability and ease-of-expression of a C-based language with the low-level control and performance of
traditional assembly language.

# The Problem to Solve
Traditionally, assembly language programs are heavily populated with a steady stream of comparisons, branches, and labels that are
used to manually implement conditional logic such as the "if", "for-next", and "do-while" constructs of higher level languages such as
C. While not a problem for simpler assembly language programs, this can negatively impact the readability and maintainability of
larger, more complex assembly language programs.

Consider the following assembly language code that displays the 8-bit integers 1 through 10, using the method WriteInt8 to do the actual
console I/O. It's fairly straight forward, so it isn't hard to follow even without comments.

        lda #1
    Loop:
        bsr WriteInt8
        inca
        cmpa #10
        blt Loop

Here's what it would look like in structured assembly language. When translated, it results in the exact same code as above; however,
you might argue that the logic is more immediately apparent.

    do (a = 1)
    {
        bsr WriteInt8
        a++
    } while (a < 10)

One thing to keep in mind in that example is that, for the assembly language code, we had to uniquely name the label "Loop"
such that it wouldn't clash with any other labels in the program. You might imagine that in a program with many comparisons and loops, coming up
with unique branch labels for all of them can get tricky to manage. For the structured assembly language code, though,
the SAL translator handles the creation and naming of all necessary branch labels.

Let's do one more example, with just a little extra complexity. As we count through our integers, let's skip any that are multiples of
the number 4. We can check that by looking at the two least significant bits of the number. If they're both 0, it's a multiple of 4.
Since the code is getting a little more complicated, we'll add some comments.

        lda #1            ; Our first integer to display.
    Loop:
        bita #%00000011   ; Examine the 2 low bits to see if the number is a multiple of 4.
        beq SkipByte      ; Skip it if it is.
        bsr WriteInt8     ; Otherwise, write the integer value to the console.
    SkipByte:
        inca              ; Advance to the next integer.
        cmpa #10          ; Continue our loop if it is less than or equal to 10.
        blt Loop          ;

And now here it is in SAL, once again being translated into the exactly the same code as as above.

    // Start at 1, and we'll loop through 10.
    do (a = 1)
    {
        // If the integer isn't a multiple of 4, write it to the console.
        if (cc.notzero(a & %00000011))
        {
            bsr WriteInt8
        }

        a++
    } while (a < 10)

Even though we've not yet ventured into anything terribly complicated, we can see that SAL let's us express the logic in a manner
similar to high-level languages, yet we still have full control over the code that is produced. Additionally, because the logic is more
apparent, the SAL code doesn't generally require the level of commenting typically done in assembly language program.
