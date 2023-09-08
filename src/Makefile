#
# Makefile for etherdfs, requires Open Watcom v2
# Copyright (C) 2017 Mateusz Viste
# Copyright (C) 2021 Davide Bresolin
#
# http://etherdfs.sourceforge.net
#

!ifeq TARGET PC9821
FLAGS=-d__PC9821__
ADD=_pc98
!else ifeq TARGET FMTOWNS
FLAGS=-d__FMTOWNS__
ADD=_fmt
!endif

# Compiler settings to optimize for execution time
CFLAGS=-0 -s -d0 -ms -wx -ot -DINTSIG_OFF=26

# Compiler settings to optimize for memory footprint
# CFLAGS=-0 -s -d0 -ms -wx -os -DINTSIG_OFF=22

# -0      generate code for 8086
# -s      disable stack overflow checks
# -d0     don't include debugging information
# -ms     small memory model
# -wx     set warning level to max
# -os     optimize for size
# -ot     optimize for execution time

all: etherdfs$(ADD).exe

chint.obj: chint086.asm
	wasm -0 chint086.asm -fo=chint.obj -ms
	
resident.obj: resident.c chint.h dosstruc.h globals.h
	wcc $CFLAGS resident.c -fo=resident.obj $(FLAGS)
	
etherdfs.obj: etherdfs.c chint.h dosstruc.h globals.h version.h
	wcc $CFLAGS etherdfs.c -fo=etherdfs.obj $(FLAGS)

etherdfs$(ADD).exe: etherdfs.obj chint.obj resident.obj
	wlink system dos file chint.obj, resident.obj,etherdfs.obj option map=etherdfs name etherdfs$(ADD) order clname rdata clname code segment begtext segment _text clname far_data clname begdata
	upx -9 --8086 etherdfs$(ADD).exe

# system dos    compile to a DOS real-mode application
# option map=   generate a map file
# name          set output file name
# order         set order of program segments

clean: .symbolic
	rm -f etherdfs$(ADD).exe
	rm -f etherdfs.map
	rm *.obj
	rm *.err

pkg: .symbolic etherdfs$(ADD).exe
	rm -f etherdfs.zip
	zip -9 -j -l etherdfs.zip ../etherdfs.txt ../history.txt
	zip -9 -j etherdfs.zip etherdfs$(ADD).exe 
	rm -f ethersrc.zip
	zip -9 -j ethersrc.zip *.h *.c *.asm ../*.txt Makefile

release: .symbolic etherdfs$(ADD).exe
	cp etherdfs$(ADD).exe ../bin
