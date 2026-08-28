#
#   Builds the SAL samples.
#
#   Assumptions: - SAL4LWTools.exe is either in the path or its directory is referenced by the
#                  environment variable SAL4LWToolsPath. (No trailing "\" in the path.)
#                - The LWTools executables are either in the path or its directory is referenced by the
#                  environment variable LWToolsPath.
#

!IFNDEF SAL4LWToolsPath
!MESSAGE SAL4LWToolsPath environment variable is not set. Let's hope SAL4LWTools is in the path!
!ENDIF

!IFNDEF LWToolsPath
!MESSAGE LWToolsPath environment variable is not set. Let's hope LWASM is in the path!
!ENDIF

!IF "$(LWToolsPath)" == ""
!MESSAGE LWToolsPath=$(LWToolsPath)
LWASM=lwasm.exe
LWLINK=lwlink.exe
!ELSE
!MESSAGE LWToolsPath=$(LWToolsPath)
LWASM="$(LWToolsPath)\lwasm.exe"
LWLINK="$(LWToolsPath)\lwlink.exe"
!ENDIF

!IF "$(SAL4LWToolsPath)" == ""
!MESSAGE SAL4LWToolsPath=$(SAL4LWToolsPath)
SAL=sal4lwtools.exe
!ELSE
!MESSAGE SAL4LWToolsPath=$(SAL4LWToolsPath)
SAL="$(SAL4LWToolsPath)\sal4lwtools.exe"
!ENDIF

SALFLAGS =
ASMFLAGS = --obj
LINKFLAGS = --decb
RAWFLAGS = --raw
BINDIR = $(ProjectDir)bin
OBJDIR = $(ProjectDir)obj

#Easy building.
#
all: $(BINDIR)\ReverseText.bin

$(BINDIR)\ReverseText.bin:
    "$(SAL)" translate -al -usesallib -od=$(OBJDIR) src\ReverseText.sal

# Manual building.
#
#all: $(BINDIR)/RVRSTEXT.BIN
#$(OBJDIR)\ReverseText.out: src/ReverseText.sal
#	$(SAL) translate $(SALFLAGS) -of=$(OBJDIR)/ReverseText.asm src/ReverseText.sal
#	$(LWASM) $(ASMFLAGS) -o $(OBJDIR)/ReverseText.out $(OBJDIR)/ReverseText.asm --list=$(OBJDIR)/ReverseText.lst
#
#$(BINDIR)\RVRSTEXT.BIN: $(OBJDIR)/ReverseText.out
#	@if not exist "$(BINDIR)" mkdir "$(BINDIR)"
#	$(LWLINK) $(LINKFLAGS) -o $(BINDIR)/RVRSTEXT.BIN $(OBJDIR)/ReverseText.out --map=$(OBJDIR)/RVRSTEXT.map

clean:
	-del /q $(OBJDIR)\*
    -del /q $(BINDIR)\*