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


can be written like this:

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

After months of experimenting with language syntax and the ANTLR 4 parser generator, I've settled on an initial C-like language definition that I think works well for 6809 assembly language programming, and I've put together a release that will let you play with SAL and explore what it can bring to your coding experience. You are welcome to use this release to whatever extent helps you get the most enjoyment from your assembly language programming. Something to keep in mind is that the language is evolving; SAL code that builds properly today might need to be updated to build with later releases.

Assembly languages tend to offer instructions and addressing modes that don't cleanly map to the common set of statements and operators found in C-like languages. These early releases reflect my initial attempts at mapping those wild-and-wonderful assembly language elements to SAL statements and operators, done "in the spirit" of C-like languages. Whether you love or loathe the choices I've made, your input is welcome!

Although some elements of this SAL implementation necessarily reflect the capabilities of the 6809 microprocessor, it can still serve as the basis for SAL design discussions in general. One of my design goals has been to define a base language that is relatively processor agnostic, with processor-specific details layering on top of that base, including the register set, addressing modes, and other unique features. Although SAL's C-like nature might make it *easier* to port code from one platform to another, it isn't intended to be a high-level language that hides away the processor details. Instead, it is intended to be an expression of assembly language that makes coding more intuitive, maintainable, and fun.


# Documentation Content

[SAL Introduction](Documentation/SAL-Intro.md)

How to Install

How to Use SAL4LWTools

How to Write SAL Code

SAL Details
