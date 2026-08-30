#
#   Builds the SAL samples.
#
#   Assumptions: - SAL4LWTools.exe is either in the path or its directory is referenced by the
#                  environment variable SAL4LWToolsPath. (No trailing "\" in the path.)
#                - The LWTools executables are either in the path or its directory is referenced by the
#                  environment variable LWToolsPath.
#

# Determine executable path for SAL4LWTools.
# Note that the environment variable SAL4LWToolsPath is essential when assembling/linking this sample
# manually, so that the location of the SAL library can be specified. If using SAL4LWTools to automatically
# assemble and link the executable, however, SAL4LWTools automatically determine the correct location of the
# library.
!IFNDEF SAL4LWTOOLSPATH
!MESSAGE SAL4LWToolsPath environment variable is not set. Let's hope SAL4LWTools is in the path!
!ENDIF

!IF "$(SAL4LWTOOLSPATH)" == ""
!MESSAGE SAL4LWTOOLSPATH=$(SAL4LWTOOLSPATH)
SAL=sal4lwtools.exe
!ELSE
!MESSAGE SAL4LWTOOLSPATH=$(SAL4LWTOOLSPATH)
SAL="$(SAL4LWTOOLSPATH)\sal4lwtools.exe"
!ENDIF

# Determine executable path for LWTools.
# This is only necessary when building manually, since SAL4LWTools internally does a similar
# determination when it launches LWASM and LWLINK automatically.
!IFNDEF LWTOOLSPATH
!MESSAGE LWToolsPath environment variable is not set. Let's hope LWASM is in the path!
!ENDIF

!IF "$(LWTOOLSPATH)" == ""
!MESSAGE LWTOOLSPATH=$(LWTOOLSPATH)
LWASM=lwasm.exe
LWLINK=lwlink.exe
!ELSE
!MESSAGE LWTOOLSPATH=$(LWTOOLSPATH)
LWASM="$(LWTOOLSPATH)\lwasm.exe"
LWLINK="$(LWTOOLSPATH)\lwlink.exe"
!ENDIF

SALFLAGS =
ASMFLAGS = --obj
LINKFLAGS = --decb
RAWFLAGS = --raw
BINDIR = $(ProjectDir)bin
OBJDIR = $(ProjectDir)obj

#Easy building.
#
all: $(OBJDIR)\ReverseText.bin
$(OBJDIR)\ReverseText.bin:
    "$(SAL)" translate -al -usesallib -od=$(OBJDIR) src\ReverseText.sal

# Manual building.
#
#all: $(BINDIR)/RVRSTEXT.BIN
#$(OBJDIR)\ReverseText.out: src/ReverseText.sal
#    $(SAL) translate $(SALFLAGS) -of=$(OBJDIR)/ReverseText.asm src/ReverseText.sal
#    $(LWASM) $(ASMFLAGS) -o $(OBJDIR)/ReverseText.out $(OBJDIR)/ReverseText.asm --list=$(OBJDIR)/ReverseText.lst
#
#$(BINDIR)\RVRSTEXT.BIN: $(OBJDIR)/ReverseText.out
#    @if not exist "$(BINDIR)" mkdir "$(BINDIR)"
#    $(LWLINK) $(LINKFLAGS) -o $(BINDIR)/RVRSTEXT.BIN $(OBJDIR)/ReverseText.out --map=$(OBJDIR)/RVRSTEXT.map --library=:SALLib.lib --library-path=$(SAL4LWTOOLSPATH)\SALLib\lib

clean:
    -del /q $(OBJDIR)\*
    -del /q $(BINDIR)\*