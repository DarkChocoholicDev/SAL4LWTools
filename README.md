# SAL4LWTools
A Structured Assembly Language (SAL) translator for the Motorola 6809* microprocessor, targeting the LWTools assembler and linker.

SAL4LWTools translates structured assembly language source files into standard 6809 assembly language, helping to blend the convenience and clarity of C-like languages with the low-level control and performance of traditional assembly language.

With SAL, code like this:

    ;
    ; Display the numbers 1 to 10.
    ;
        lda #1          ; First number to display.
    Loop:
        bsr WriteInt8   ; Display the number.
        inca            ; Advance to the next number.
        cmpa #10        ; Have we displayed the final number?
        ble Loop        ; Loop if not.


can instead be written like this:

    //
    // Display the numbers 1 to 10.
    //
    do (a = 1)
    {
        WriteInt8();
        a++;
    } while (a <= 10);


\*The full 6309 instruction set will eventually be supported.

# Project Status

Welcome to the "proof-of-concept" stage of SAL4LWTools!

After months of experimenting with language syntax and the ANTLR 4 parser generator, I've settled on an initial C-like language definition that I think works well for 6809 assembly language programming, and I've put together a release that will let you play with SAL and explore what it can bring to your coding experience. You are welcome to use this release to whatever extent helps you get the most enjoyment from your assembly language programming. Something to keep in mind, though, is that the language is evolving; so, SAL code that builds properly with current releases may need to be updated to work with later releases.

Assembly languages tend to offer have operations and addressing modes that don't map cleanly to the operators found in C-like languages. The proof-of-concept releases attempt to map some of those wild-and-wonderful assembly language elements to SAL operations "in the spirit" of C-like languages. Whether you love or loathe the choices I've made, your input is welcome!

Although some elements of this SAL implementation necessarily reflect the capabilities of the 6809 microprocessor, I'm sure it can serve as the basis for SAL design discussions in general. One of my design goals is to define a core language that is relatively processor agnostic, but that can then be tailored to fully express the features of the target processor. SAL isn't intended to be a high-level language that makes code portable across processor architectures. Rather, it is intended to be an expression of assembly language that makes coding more intuitive, maintainable, and fun.


# Documentation Content

SAL Introduction

How to Install

How to Use SAL4LWTools

How to Write SAL Code

SAL Details







# The Problem to Solve
Traditionally, assembly language programs are heavily populated with a steady stream of comparisons, branches, and labels that are used to manually implement conditional logic such as the "if", "for-next", and "do-while" constructs of higher level languages such as C. While not a problem for simpler assembly language programs, this can negatively impact the readability and maintainability of larger, more complex assembly language programs.

Consider the following assembly language code that displays the 8-bit integers 1 through 10, using the method WriteInt8 to do the actual console I/O. It's fairly straight forward, so it isn't hard to follow even without comments.

        lda #1
    Loop:
        bsr WriteInt8
        inca
        cmpa #10
        blt Loop

Here's what it would look like in structured assembly language. When translated, it results in the exact same code as above; however, you might argue that the logic is more immediately apparent.

    do (a = 1)
    {
        bsr WriteInt8
        a++
    } while (a < 10)

One thing to keep in mind in that example is that, for the assembly language code, we had to uniquely name the label "Loop" such that it wouldn't clash with any other labels in the program. You might imagine that in a program with many comparisons and loops, coming up with unique branch labels for all of them can get tricky to manage. For the structured assembly language code, though, the SAL translator handles the creation and naming of all necessary branch labels.

Let's do one more example, with just a little extra complexity. As we count through our integers, let's skip any that are multiples of the number 4. We can check that by looking at the two least significant bits of the number. If they're both 0, it's a multiple of 4. Since the code is getting a little more complicated, we'll add some comments.

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

Even though we've not yet ventured into anything terribly complicated, we can see that SAL let's us express the logic in a manner similar to high-level languages, yet we still have full control over the code that is produced. Additionally, because the logic is more apparent, the SAL code doesn't generally require the level of commenting typically done in assembly language program.
