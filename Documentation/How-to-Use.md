**... WORK IN PROGRESS! ...**

# How to Use the SAL4LWTools
SAL4LWTools is a command-line-based utility that translates structured assembly language source files into standard assembly language for use with the LWTools assembly language toolchain.

You can either use SAL4LWTools as a translation-only step in your existing build process, or you can use SAL4LWTools to both translate the source files and automatically call out to LWASM and LWLINK to build your assembly language program in one step.

This documentation will cover both ways to use SAL4LWTools.

**NOTE:** Command examples given in this document assume that SAL4LWTools has been installed such that SAL4LWTools.exe is in the executable search path.

## Using SAL4LWTools to Just Translate
To translate a SAL source file to standard assembly language, use the "translate" command without the "assemble" (-a) or "assemble and link" (-al) options.

### Examples

Translates the file MyApp.sal to output\MyApp.asm:

    sal4lwtools translate MyApp.asm

Translates the file Hello.sal to Test.asm:

    sal4lwtools translate Hello.sal -of=Test.asm

Translates the files Main.sal and Util.sal to obj\Main.asm and obj\Util.asm:

    sal4lwtools translate Main.sal Util.sal -od=obj

## Using SAL4LWTools To Translate and Assemble
To translate a SAL source files and automatically run LWASM to assemble it, use the "assemble" (-a) option. To have SAL both assemble *and* link the file, use the "assemble and link" (-al) option.

**NOTE:** When used in this way, SAL4LWTools needs to conform to the "section" requirements imposed by the LWASM and LWLINK, depending on whether the program is made up of a single SAL file or multiple SAL files. This documentation draft doesn't currently cover that topic, but it will eventually be added.

### Examples

Translates the file MyApp.sal and uses LWASM and LWLINK to produce MyApp.bin. All generated files will be created in the directory named "output" and the SALLib runtime library is not used:

    sal4lwtools translate MyApp.sal -al

Translates the file SALConsoleApp.sal and uses LWASM and LWLINK to produce SALConsoleApp.bin, using the SALLib runtime library. All generated files will be created in the directory named "obj".

    sal4lwtools translate SALConsoleApp.sal -usesallib

**... WORK IN PROGRESS! ...**