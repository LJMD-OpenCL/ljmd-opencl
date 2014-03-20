
# OS Name (Linux or Darwin)
OSUPPER = $(shell uname -s 2>/dev/null | tr [:lower:] [:upper:])
OSLOWER = $(shell uname -s 2>/dev/null | tr [:upper:] [:lower:])

# Flags to detect 32-bit or 64-bit OS platform
OS_SIZE = $(shell uname -m | sed -e "s/i.86/32/" -e "s/x86_64/64/")
OS_ARCH = $(shell uname -m | sed -e "s/i386/i686/")

# Flags to detect either a Linux system (linux) or Mac OSX (darwin)
DARWIN = $(strip $(findstring DARWIN, $(OSUPPER)))

CC             := g++

# OS-specific build flags
ifneq ($(DARWIN),) 
      CCFLAGS   := -arch $(OS_ARCH) 
else
  ifeq ($(OS_SIZE),32)
      CCFLAGS   := -m32
  else
      CCFLAGS   := -m64
  endif
endif

TOP      = $(shell pwd)
LCL_OBJDIR = objs
LCL_DOCDIR = docs

EXE         := $(TOP)/ljmd-vis
LJMDEXE     := ../ljmd-cl
XYZFILE     := argon_108.xyz

LCL_SOURCES := $(TOP)/ljmd-memhand.cpp $(TOP)/ljmd-povtools.cpp $(TOP)/ljmd-vis.cpp

LCL_HEADERS := $(TOP)/ljmd-memhand.h $(TOP)/ljmd-povtools.h

LCL_OBJS    := $(LCL_OBJDIR)/ljmd-memhand.o  $(LCL_OBJDIR)/ljmd-povtools.o \
               $(LCL_OBJDIR)/ljmd-trajclass.o $(LCL_OBJDIR)/ljmd-vis.o


all: objdir build

build: $(LCL_OBJS) $(EXE)

$(LCL_OBJDIR)/ljmd-memhand.o: $(TOP)/ljmd-memhand.cpp $(TOP)/ljmd-memhand.h
	$(CC) $(CCFLAGS) -o $@ -c $<

$(LCL_OBJDIR)/ljmd-povtools.o: $(TOP)/ljmd-povtools.cpp $(TOP)/ljmd-povtools.h
	$(CC) $(CCFLAGS) -o $@ -c $<

$(LCL_OBJDIR)/ljmd-trajclass.o: $(TOP)/ljmd-trajclass.cpp $(TOP)/ljmd-trajclass.h
	$(CC) $(CCFLAGS) -o $@ -c $<

$(LCL_OBJDIR)/ljmd-vis.o: $(TOP)/ljmd-vis.cpp
	$(CC) $(CCFLAGS) -o $@ -c $<

$(XYZFILE): $(LJMDEXE)
	@ln -sF ../examples/argon_108.inp argon_108.inp
	@ln -sF ../examples/argon_108.rest argon_108.rest
	$(LJMDEXE) cpu 8 < argon_108.inp

$(EXE): $(LCL_OBJS)
	$(CC) $(LCL_OBJS) -o $(EXE)

documents:
	cd $(LCL_DOCDIR); doxygen Doxyfile

clean:
	rm -f $(LCL_OBJDIR)/*.o $(TOP)/*.jpg output.avi $(EXE)
	rm -rf  $(LCL_DOCDIR)/html/*
	rm -rf argon*.* *.pov

samplevideo: $(EXE) $(XYZFILE)
	$(EXE) $(XYZFILE)
	make video

objdir:
	mkdir -p $(LCL_OBJDIR)

$(LJMDEXE): 
	cd ..; make; cd $(TOP)

video:
	mencoder mf://*.jpg -mf w=600:h=400:fps=6:type=jpg -ovc lavc\
        -lavcopts vcodec=mpeg4:mbd=2:trell -oac copy -o output.avi  >/dev/null 2>&1
