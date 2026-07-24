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

SAL=sal4lwtools.exe

SALFLAGS =
ASMFLAGS = --obj
LINKFLAGS = --decb
RAWFLAGS = --raw
BINDIR = $(ProjectDir)bin
OBJDIR = $(ProjectDir)obj

all: $(BINDIR)/RVRSTEXT.BIN
    @echo Current directory:
    @cd
    @echo ProjectDir: "$(ProjectDir)"

$(OBJDIR)\ReverseText.out: src/ReverseText.sal
	$(SAL) translate $(SALFLAGS) -of=$(OBJDIR)/ReverseText.asm src/ReverseText.sal
	$(LWASM) $(ASMFLAGS) -o $(OBJDIR)/ReverseText.out $(OBJDIR)/ReverseText.asm --list=$(OBJDIR)/ReverseText.lst

$(BINDIR)\RVRSTEXT.BIN: $(OBJDIR)/ReverseText.out
	@if not exist "$(BINDIR)" mkdir "$(BINDIR)"
	$(LWLINK) $(LINKFLAGS) -o $(BINDIR)/RVRSTEXT.BIN $(OBJDIR)/ReverseText.out --map=$(OBJDIR)/RVRSTEXT.map

clean:
	-del /q $(OBJDIR)\*
    -del /q $(BINDIR)\*