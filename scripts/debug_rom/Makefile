# See LICENSE.SiFive for license details
# Recursive make is bad, but in this case we're cross compiling which is a
# pretty unusual use case.

CC = $(RISCV)/bin/riscv64-unknown-elf-gcc
OBJCOPY = $(RISCV)/bin/riscv64-unknown-elf-objcopy

COMPILE = $(CC) -nostdlib -nostartfiles -I$(RISCV)/riscv64-unknown-elf/include/ -Tlink.ld

ELFS = debug_rom debug_rom_nonzero

all: $(ELFS)

publish: DebugRomContents.scala DebugRomNonzeroContents.scala
	mv $^ ../../src/main/scala/devices/debug

%.scala: %.raw
	xxd -i $^ | sed -e "s/^unsigned char DebugRom.*/\/\/ This file was auto-generated by 'make publish' in debug\/ directory.\n\npackage freechips.rocketchip.devices.debug\n\nobject $(basename $<) {\n\n  def apply() : Array[Byte] = { Array (/" \
	-e  "s/};/  ).map(_.toByte) }\n\n}/" \
	-e  "s/^unsigned int DebugRom.*//" > $@

DebugRomContents.raw: debug_rom
	$(OBJCOPY) -O binary --only-section .text $^ $@

DebugRomNonzeroContents.raw: debug_rom_nonzero
	$(OBJCOPY) -O binary --only-section .text $^ $@

%: %.S
	$(COMPILE) -o $@ $^

clean:
	rm -f $(ELFS) DebugRom*.raw
