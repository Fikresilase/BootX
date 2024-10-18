# Source directories
SRC_DIRS = kernel drivers

# Automatically gather source and header files using wildcard.
C_SRC_FILES = $(wildcard $(SRC_DIRS)/*.c)
H_HEADER_FILES = $(wildcard $(SRC_DIRS)/*.h)

# Object files derived from C source files.
OBJ_FILES = ${C_SRC_FILES:.c=.o}

# Targets for clean, build, and run processes.
all: clean os-image execute

# Clean up built files.
clean:
	rm -f boot/*.bin
	rm -f drivers/*.o
	rm -f example/*.bin example/*.o
	rm -f kernel/*.bin kernel/*.o
	rm -f os-image

# Example targets for demonstration and learning purposes.

# Run a simple boot sector example.
minimal_example:
	qemu-system-x86_64 -drive file=example/minimal,format=raw -net none

# Boot sector examples.
boot_example: example/boot_sector.bin
	qemu-system-x86_64 -drive file=$<,format=raw -net none

# Other boot sectors demonstrating various functionalities.
find_byte_example: example/find_the_byte.bin
	qemu-system-x86_64 -drive file=$<,format=raw -net none

hello_simple: example/hello_simple.bin
	qemu-system-x86_64 -drive file=$<,format=raw -net none

hello_advanced: example/hello_advanced.bin
	qemu-system-x86_64 -drive file=$<,format=raw -net none

# Generic example for additional real mode demonstrations.
%_example: example/%_example.bin
	qemu-system-x86_64 -drive file=$<,format=raw -net none

# Protected mode boot sector example.
switch_to_pm: example/boot_to_pm.bin
	qemu-system-x86_64 -drive file=$<,format=raw -net none

# Disassembly examples.
%_disassemble: example/%_disassemble.bin
	ndisasm -b 32 $<

example/%_disassemble.bin: example/%_disassemble.o
	x86_64-elf-ld -melf_i386 -o $@ -Ttext 0x0 --oformat binary $<

# Kernel building process.

# Create the kernel binary by linking its components.
kernel/kernel.bin: kernel/enter_kernel.o kernel/kernel.o
	x86_64-elf-ld -melf_i386 -o $@ -Ttext 0x1000 --oformat binary $^

# Construct the full OS image by prepending the boot sector.
os-image: boot/load_kernel.bin kernel/kernel.bin
	cat $^ > $@

# Compile assembly source files into binary format.
%.bin: %.asm
	nasm $< -f bin -o $@

# Assemble assembly source files to object files.
%.o: %.asm
	nasm $< -f elf -o $@

# Compile C source files into object files.
%.o: %.c
	x86_64-elf-gcc -m32 -ffreestanding -c $< -o $@

# Run the operating system on an emulated x86 machine.
execute: os-image
	qemu-system-x86_64 -drive file=$<,format=raw -net none