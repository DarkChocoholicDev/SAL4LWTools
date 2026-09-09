# SAL4LWTools
A Structured Assembly Language (SAL) translator for the Motorola 6809* microprocessor, targeting the LWTools assembler and linker.

SAL4LWTools is a command line utility that plugs into your assembly language build process, enabling you to write your programs in *structured* assembly language, making them easier to create and maintain.

Code that once looked like this:

    ;
    ; Display the numbers 1 to 10.
    ;
        lda #1          ; First number to display.
    Loop:
        bsr WriteInt8   ; Display the number.
        inca            ; Advance to the next number.
        cmpa #10        ; Have we displayed the final number?
        ble Loop        ; Loop if not.


can now look like this:

    //
    // Display the numbers 1 to 10.
    //
    do (a = 1)
    {
        WriteInt8();
        a++;
    } while (a <= 10);


\*The full 6309 instruction set will eventually be supported.

# Licensing
Although my current desire is to release SAL4LWTools as open source in a later stage of the project, for now I'm releasing it as a binary package along with sample code and the SALLib runtime library. I'm still working on the appropriate LICENSE files to include within the repo and inside the release package. For now, though, here is a run-down of the intended licensing.

**SAL4LWTools:** Copyright © Keith Frechette. All rights reserved. The SAL4LWTools application is currently distributed as freeware; its source code is not currently released under an open-source license.

**SAL Library:** Licensed under the Zero-Clause BSD (0BSD) license. SALLib code may be freely incorporated into programs, including commercial programs, without attribution.

**Sample Code:** Licensed under the Zero-Clause BSD (0BSD) license. Sample code may be freely copied, modified, and incorporated into your own programs without attribution.

Programs created using SAL4LWTools are the property of their respective authors and are not subject to the SAL4LWTools license merely because SAL4LWTools was used to translate them.


# Project Status
Welcome to the "proof-of-concept" phase of SAL4LWTools!

After months of experimenting with language syntax and the ANTLR 4 parser generator, I've settled on an initial C-like language definition that I think works well for 6809 assembly language programming, and I've put together a release that will let you play with SAL and explore what it can bring to your coding experience. You are welcome to use this release to whatever extent helps you get the most enjoyment from your assembly language programming. Something to keep in mind is that the language is evolving; SAL code that builds properly today might need to be updated to build with later releases. Community feedback will help steer the direction of the language.

Assembly languages tend to offer instructions and addressing modes that don't always map cleanly to the common set of statements and operators found in C-like languages. These early releases reflect my initial attempts to map those wild-and-wonderful assembly language elements to SAL statements and operators, done "in the spirit of" C-like languages. Whether you love or loathe the choices I've made, your input is welcome!

Although some elements of this SAL implementation necessarily reflect the capabilities of the 6809 microprocessor, it can still serve as the basis for SAL design discussions in general. One of my design goals has been to define a base language that is relatively processor agnostic, with processor-specific details layering on top of that base, including the register set, addressing modes, and other unique features. Although SAL's C-like nature might make it *easier* to port code from one platform to another, it isn't intended to be a high-level language that hides away the processor details. Instead, it is intended to be an expression of assembly language that makes coding more intuitive, maintainable, and fun.


# Documentation Content

[SAL Introduction](Documentation/SAL-Intro.md)

[How to Install](Documentation/How-to-Install.md)

[How to Use SAL4LWTools](Documentation/How-to-Use.md)

How to Write SAL Code

SAL Details
