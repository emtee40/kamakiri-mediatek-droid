CC := arm-none-eabi-gcc
AS := arm-none-eabi-as
LD := arm-none-eabi-gcc
OBJCOPY := arm-none-eabi-objcopy

CFLAGS := -std=gnu99 -Os -mthumb -mcpu=cortex-a9 -fno-builtin-printf -fno-strict-aliasing -fno-builtin-memcpy -mno-unaligned-access -Wall -Wextra
LDFLAGS := -nodefaultlibs -nostdlib -lgcc

STAGE1 := stage1
STAGE2 := stage2
PL := pl

STAGE1_SRC = stage1.c brom_common.c common.c
STAGE2_SRC = stage2.c brom_common.c common.c payload_common.c libc.c printf.c drivers/sd.c drivers/mmc.c crypto/sha256.c crypto/hmac-sha256.c
PL_SRC = pl.c common.c payload_common.c libc.c printf.c drivers/sd.c drivers/mmc.c crypto/sha256.c crypto/hmac-sha256.c
ASM_SRC = start.S

OBJ = $(STAGE1_SRC:%.c=$(STAGE1)/%.o) $(ASM_SRC:%.S=$(STAGE1)/%.o)
DEP = $(OBJ:%.o=%.d)

STAGE2_OBJ = $(STAGE2_SRC:%.c=$(STAGE2)/%.o) $(ASM_SRC:%.S=$(STAGE2)/%.o)
STAGE2_DEP = $(STAGE2_OBJ:%.o=%.d)

PL_OBJ = $(PL_SRC:%.c=$(PL)/%.o) $(ASM_SRC:%.S=$(PL)/%.o)
PL_DEP = $(PL_OBJ:%.o=%.d)

all: $(STAGE1)/$(STAGE1).bin $(STAGE2)/$(STAGE2).bin $(PL)/$(PL).bin

$(STAGE1)/$(STAGE1).bin: $(STAGE1)/$(STAGE1).elf
	$(OBJCOPY) -O binary $^ $@

$(STAGE2)/$(STAGE2).bin: $(STAGE2)/$(STAGE2).elf
	$(OBJCOPY) -O binary $^ $@

$(PL)/$(PL).bin: $(PL)/$(PL).elf
	$(OBJCOPY) -O binary $^ $@

$(STAGE1)/$(STAGE1).elf: $(OBJ)
	$(LD) -o $@ $^ $(LDFLAGS) -T $(STAGE1).ld

$(STAGE2)/$(STAGE2).elf: $(STAGE2_OBJ)
	$(LD) -o $@ $^ $(LDFLAGS) -T $(STAGE2).ld

$(PL)/$(PL).elf: $(PL_OBJ)
	$(LD) -o $@ $^ $(LDFLAGS) -T $(PL).ld

-include $(DEP)

$(STAGE1)/%.o: %.c
	mkdir -p $(@D)
	$(CC) -MMD -c -o $@ $< $(CFLAGS)

$(STAGE1)/%.o: %.S
	mkdir -p $(@D)
	$(AS) -o $@ $<

-include $(STAGE2_DEP)

$(STAGE2)/%.o: %.c
	mkdir -p $(@D)
	$(CC) -MMD -c -o $@ $< $(CFLAGS)

$(STAGE2)/%.o: %.S
	mkdir -p $(@D)
	$(AS) -o $@ $<

-include $(PL_DEP)

$(PL)/%.o: %.c
	mkdir -p $(@D)
	$(CC) -MMD -c -o $@ $< $(CFLAGS)

$(PL)/%.o: %.S
	mkdir -p $(@D)
	$(AS) -o $@ $<


clean:
	-rm -rf $(STAGE1)
	-rm -rf $(STAGE2)
	-rm -rf $(PL)
