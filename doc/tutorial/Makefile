ifeq ($(OS), Windows_NT)
WHICH=where
else
WHICH=which
SMUDGE ?= $(shell find ../.. -print | grep 'smudge/.stack-work/install' | grep 'bin/smudge')
endif

ifeq ($(SMUDGE),)
SMUDGE=$(shell $(WHICH) smudge)
endif

ifeq ($(wildcard /usr/include/smear), "")
SMEAR_ROOT ?= ../../smear
LPATH=-L$(SMEAR_ROOT)
IPATH=-I$(SMEAR_ROOT)/include
endif

CFLAGS=-Wall -Werror -Wextra -Wno-unused-parameter -ggdb3
LFLAGS=-lsmear -pthread -lrt
SMUDGE_OPTIONS_PNG=--dot-fmt=Png --c-smear
CC=gcc


SMUDGE_FILES=$(basename $(wildcard *.smudge))
TEST_LOGS=$(foreach x, $(SMUDGE_FILES), $(x).log)
TEST_TARGETS=$(foreach x, $(SMUDGE_FILES), $(x)-test)
ALWAYS_GENERATED=.h .c _ext.h
GENERATED_EXTENSIONS=$(ALWAYS_GENERATED) _ext.c .gv .png .svg
GENERATED_FILES=$(foreach x, $(GENERATED_EXTENSIONS), $(addsuffix $(x), $(SMUDGE_FILES)))
GEN_DOC=$(foreach x, $(ALWAYS_GENERATED), $(addsuffix $(x), $(SMUDGE_FILES)))

.PHONY: clean all default check

default: all

all: 00_pinball 01_pinball 02_pinball 03_pinball 04_pinball 05_pinball 06_pinball 07_pinball 08_pinball 09_pinball 10_pinball

%.png %_ext.h %.c %.h: %.smudge
	$(SMUDGE) $(SMUDGE_OPTIONS_PNG) $^

pinball-tutorial.pdf: pinball-tutorial.rst 00_pinball.png *_main.c *.smudge $(GEN_DOC)
	rst2pdf $<

%.log: %
	./$< > $@ 2>&1 || true

%_pinball-test: %_pinball.log
	diff -q $< $<.golden

check: $(TEST_TARGETS)

%_main.o: %_main.c %_pinball.h
	$(CC) $(CFLAGS) $(IPATH) -c -o $@ $<

%.o: %.c
	$(CC) $(CFLAGS) $(IPATH) -c -o $@ $<

%_pinball: %_pinball.o %_main.o %_pinball.h %_pinball_ext.h
	$(CC) $(LPATH) -o $@ $(filter %.o, $^) $(LFLAGS)

clean:
	rm -f $(GENERATED_FILES) *.o *_pinball *.pdf *.log
