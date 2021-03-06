ifndef CROSS_COMPILE
CROSS_COMPILE = or32-uclinux-
CC = $(CROSS_COMPILE)gcc
LD = $(CROSS_COMPILE)ld
NM = $(CROSS_COMPILE)nm
endif

export	CROSS_COMPILE

all: sdTest.intel.hex

reset.o: reset.S Makefile
	$(CC) -g -c -o $@ $< $(CFLAGS)

sdTest.o: sdTest.c Makefile board.h
	$(CC) -g -c -o $@ $< $(CFLAGS)

sdTest.or32: reset.o sdTest.o Makefile
	$(LD) -Tram.ld -o $@ reset.o sdTest.o $(LIBS)
	$(CROSS_COMPILE)objdump -D $@ > sdTest.dis

sdTest.bin: sdTest.or32
	or32-uclinux-objcopy sdTest.or32 sdTest.bin -O binary -I elf32-or32


System.map: sdTest.or32
	@$(NM) $< | \
		grep -v '\(compiled\)\|\(\.o$$\)\|\( [aUw] \)\|\(\.\.ng$$\)\|\(LASH[RL]DI\)' | \
		sort > System.map

#########################################################################
clean:
	find . -type f \
		\( -name 'core' -o -name '*.bak' -o -name '*~' \
		-o -name '*.o'  -o -name '*.a' -o -name '*.tmp' \
		-o -name '*.or32' -o -name '*.bin' -o -name '*.srec' \
		-o -name '*.mem' -o -name '*.img' -o -name '*.out' \
		-o -name '*.aux' -o -name '*.log' -o -name '*.hex' -o \
		-name '*.init' -o -name '*.dis' \) -print \
		| xargs rm -f
	rm -f System.map

distclean: clean
	find . -type f \
		\( -name .depend -o -name '*.srec' -o -name '*.bin' \
		-o -name '*.pdf' \) \
		-print | xargs rm -f
	rm -f $(OBJS) *.bak tags TAGS
	rm -fr *.*~
