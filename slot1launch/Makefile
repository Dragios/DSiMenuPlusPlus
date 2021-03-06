#---------------------------------------------------------------------------------
.SUFFIXES:
#---------------------------------------------------------------------------------
.SECONDARY:

ifeq ($(strip $(DEVKITARM)),)
$(error "Please set DEVKITARM in your environment. export DEVKITARM=<path to>devkitARM)
endif

include $(DEVKITARM)/ds_rules

export TARGET		:=	slot1launch
export TOPDIR		:=	$(CURDIR)

export VERSION_MAJOR	:= 1
export VERSION_MINOR	:= 99
export VERSTRING	:=	$(VERSION_MAJOR).$(VERSION_MINOR)

#---------------------------------------------------------------------------------
# path to tools - this can be deleted if you set the path in windows
#---------------------------------------------------------------------------------
export PATH		:=	$(DEVKITARM)/bin:$(PATH)

.PHONY: cardengine_arm7 bootloader $(TARGET).arm7 $(TARGET).arm9 clean

#---------------------------------------------------------------------------------
# main targets
#---------------------------------------------------------------------------------
all: cardengine_arm7 bootloader $(TARGET).nds

dist:	all
	@rm	-fr	hbmenu
	@mkdir hbmenu
	@cp hbmenu.nds hbmenu/BOOT.NDS
	@cp BootStrap/_BOOT_MP.NDS BootStrap/TTMENU.DAT BootStrap/_DS_MENU.DAT BootStrap/ez5sys.bin BootStrap/akmenu4.nds hbmenu
	@tar -cvjf hbmenu-$(VERSION).tar.bz2 hbmenu testfiles README.md COPYING -X exclude.lst

$(TARGET).nds:	$(TARGET).arm7 $(TARGET).arm9
	ndstool	-c $(TARGET).nds -7 $(TARGET).arm7.elf -9 $(TARGET).arm9.elf \
	python2 patch_ndsheader_dsiware.py $(CURDIR)/$(TARGET).nds --accessControl 0x00000038
			-b $(CURDIR)/icon.bmp "TWiLight Menu++;Slot-1 Launcher;Apache Thunder, RocketRobz" \
			-g TWL1 01 "TWLMENUPP-S1" -z 80040000 -u 00030004

$(TARGET).arm7	: arm7/$(TARGET).elf
	cp arm7/$(TARGET).elf $(TARGET).arm7.elf
$(TARGET).arm9	: arm9/$(TARGET).elf
	cp arm9/$(TARGET).elf $(TARGET).arm9.elf

#---------------------------------------------------------------------------------
arm7/$(TARGET).elf:
	$(MAKE) -C arm7
	
#---------------------------------------------------------------------------------
arm9/$(TARGET).elf:
	$(MAKE) -C arm9
	
#---------------------------------------------------------------------------------
cardengine_arm7: data
	@$(MAKE) -C cardengine_arm7

#---------------------------------------------------------------------------------
clean:
	@echo clean ...
	@rm -fr data
	@rm -fr $(BUILD) $(TARGET).elf $(TARGET).nds $(TARGET).nds.orig.nds
	@rm -fr $(TARGET).arm7
	@rm -fr $(TARGET).arm9
	@rm -fr $(TARGET).arm7.elf
	@rm -fr $(TARGET).arm9.elf
	@$(MAKE) -C bootloader clean
	@$(MAKE) -C arm9 clean
	@$(MAKE) -C arm7 clean
	@$(MAKE) -C cardengine_arm7 clean

data:
	@mkdir -p data

bootloader: data
	@$(MAKE) -C bootloader
