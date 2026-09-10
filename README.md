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
Although my current desire is to release SAL4LWTools as open source at a later stage of the project, for now I'm releasing it as a binary package along with sample code and the SALLib runtime library. Appropriate LICENSE files are being added to the repository and release package; the following describes the licensing that applies to the current release.

**SAL4LWTools:** Copyright © CoolWorks Interactive, LLC. All rights reserved. The SAL4LWTools application is currently distributed as freeware; its source code is not currently released under an open-source license.

**SALLib:** Licensed under the Zero-Clause BSD (0BSD) license. SALLib code may be freely used, copied, modified, and incorporated into programs, including commercial programs, without attribution.

**Sample Code:** Licensed under the Zero-Clause BSD (0BSD) license. Sample code may be freely used, copied, modified, and incorporated into your own programs, including commercial programs, without attribution.

Programs created using SAL4LWTools are the property of their respective authors and are not subject to the SAL4LWTools license merely because SAL4LWTools was used to translate them.


# Project Status
Welcome to the "proof-of-concept" phase of SAL4LWTools!

After months of experimenting with language syntax and the ANTLR 4 parser generator, I've settled on an initial C-like language definition that I think works well for 6809 assembly language programming, and I've put together a release that will let you play with SAL and explore what it can bring to your coding experience. You are welcome to use this release to whatever extent helps you get the most enjoyment from your assembly language programming. Something to keep in mind is that the language is evolving; SAL code that builds properly today might need to be updated to build with later releases. Community feedback will help steer the direction of the language.

Assembly languages tend to offer instructions and addressing modes that don't always map cleanly to the common set of statements and operators found in C-like languages. These early releases reflect my initial attempts to map those wild-and-wonderful assembly language elements to SAL statements and operators, done "in the spirit of" C-like languages. Whether you love or loathe the choices I've made, your input is welcome!

Although some elements of this SAL implementation necessarily reflect the capabilities of the 6809 microprocessor, it can still serve as the basis for SAL design discussions in general. One of my design goals has been to define a base language that is relatively processor agnostic, with processor-specific details layering on top of that base, including the register set, addressing modes, and other unique features. Although SAL's C-like nature might make it *easier* to port code from one platform to another, it isn't intended to be a high-level language that hides away the processor details. Instead, it is intended to be an expression of assembly language that makes coding more intuitive, maintainable, and fun.


# Feedback and Discussion
SAL4LWTools is currently in the proof-of-concept phase, and your feedback can help shape the direction of the language.

Have a question about using SAL? An idea for improving the language? Love or hate one of the syntax choices? Please join the [SAL4LWTools Discussions](https://github.com/DarkChocoholicDev/SAL4LWTools/discussions).

If you've created something using SAL, I'd also love to hear about it! Share your experience—and your project, if you'd like—in the **Show and tell** discussion category.

If you've encountered a specific bug or other problem with SAL4LWTools, please [open an issue.](https://github.com/DarkChocoholicDev/SAL4LWTools/issues)


# Documentation

[SAL Introduction](Documentation/SAL-Intro.md)

[How to Install](Documentation/How-to-Install.md)

[How to Use SAL4LWTools](Documentation/How-to-Use.md)

How to Write SAL Code

SAL Details
