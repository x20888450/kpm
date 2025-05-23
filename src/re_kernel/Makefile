MYKPM_VERSION := 6.0.11

ifndef KP_DIR
    KP_DIR = ../..
endif

OS_NAME = $(shell uname | tr A-Z a-z)
MACHINE = $(shell uname -m)
NDK_BIN_DIR := toolchains/llvm/prebuilt/$(OS_NAME)-$(MACHINE)/bin
ifdef ANDROID_NDK_LATEST_HOME
    NDK_PATH ?= $(ANDROID_NDK_LATEST_HOME)/$(NDK_BIN_DIR)
else ifdef ANDROID_NDK
    NDK_PATH ?= $(ANDROID_NDK)/$(NDK_BIN_DIR)
endif

ifdef TARGET_COMPILE
    CC := $(TARGET_COMPILE)gcc
    LD := $(TARGET_COMPILE)ld
    STRIP := $(TARGET_COMPILE)strip
else ifdef NDK_PATH
    CC := $(NDK_PATH)/aarch64-linux-android31-clang
    LD := $(NDK_PATH)/ld.lld
    STRIP := $(NDK_PATH)/llvm-strip
endif

CC ?= gcc
LD ?= ld
STRIP ?= strip

CFLAGS = -Wall -O3 -fno-PIC -fno-asynchronous-unwind-tables -fno-stack-protector -fno-common -DMYKPM_VERSION=\"$(MYKPM_VERSION)$(MYKPM_VER)\"

INCLUDE_DIRS := . include patch/include linux/include linux/arch/arm64/include linux/tools/arch/arm64/include kpm/include

INCLUDE_FLAGS := $(foreach dir,$(INCLUDE_DIRS),-I$(KP_DIR)/kernel/$(dir))

objs := re_kernel.c

all: base network

base: re_kernel_$(MYKPM_VERSION).kpm

network: CFLAGS += -DCONFIG_NETWORK
network: MYKPM_VER := _n
network: re_kernel_$(MYKPM_VERSION)_network.kpm

debug: CFLAGS += -DCONFIG_DEBUG
debug: MYKPM_VER := _d
debug: re_kernel_$(MYKPM_VERSION)_debug.kpm

re_kernel_$(MYKPM_VERSION).kpm: ${objs}
	${CC} $(CFLAGS) $(INCLUDE_FLAGS) $^ -r -o $@
	${STRIP} -g --strip-unneeded $@

re_kernel_$(MYKPM_VERSION)_network.kpm: ${objs}
	${CC} $(CFLAGS) $(INCLUDE_FLAGS) $^ -r -o $@
	${STRIP} -g --strip-unneeded $@

re_kernel_$(MYKPM_VERSION)_debug.kpm: ${objs}
	${CC} $(CFLAGS) $(INCLUDE_FLAGS) $^ -r -o $@
	${STRIP} -g --strip-unneeded $@

.PHONY: clean
clean:
	rm -f *.kpm
	find . -name "*.o" | xargs rm -f
