#============================================================================
# PROJECT: gemBS
# FILE: Makefile
# DATE: 01/01/2015
# AUTHOR(S): Marcos Fernandez Callejo <mfernandez@cnag.crg.eu>
# DESCRIPTION: Top level makefile
#============================================================================

# Definitions
ROOT_PATH=$(CURDIR)

# samtools and bcftools definitions
SAMTOOLS_VERSION=1.8
BCFTOOLS_VERSION=1.8
SAMTOOLS_DIR=samtools
BCFTOOLS_DIR=bcftools
SAMTOOLS=$(SAMTOOLS_DIR)/samtools
BCFTOOLS=$(BCFTOOLS_DIR)/bcftools
SAMTOOLS_TAR=samtools-$(SAMTOOLS_VERSION).tar.bz2
BCFTOOLS_TAR=bcftools-$(BCFTOOLS_VERSION).tar.bz2
SAMTOOLS_URL=https://github.com/samtools/samtools/releases/download/$(SAMTOOLS_VERSION)/$(SAMTOOLS_TAR)
BCFTOOLS_URL=https://github.com/samtools/bcftools/releases/download/$(BCFTOOLS_VERSION)/$(BCFTOOLS_TAR)

UNAME := $(shell uname -s)
ifeq ($(UNAME),Linux)
  ARCHIVE=http://hgdownload.cse.ucsc.edu/admin/exe/linux.x86_64
endif
ifeq ($(UNAME),Darwin)
  ARCHIVE=http://hgdownload.cse.ucsc.edu/admin/exe/macOSX.x86_64
endif

		  
FOLDER_BIN=bin

all: setup gem3 _samtools _bcftools kent _bs_call _utils

all_static: setup gem3-static _samtools _bcftools kent _bs_call _utils

all_debug: setup gem3-debug _samtools _bcftools kent _bs_call _utils

_samtools: $(SAMTOOLS_DIR) $(SAMTOOLS_DIR)/config.mk 
	$(MAKE) --directory=$(SAMTOOLS_DIR) all all-htslib

_bcftools: $(BCFTOOLS_DIR) $(BCFTOOLS_DIR)/config.h
	$(MAKE) $(FOLDER_BIN)/bcftools

kent: $(FOLDER_BIN)/wigToBigWig $(FOLDER_BIN)/bedToBigBed

_bs_call: bs_call/src/Makefile.mk
	$(MAKE) --directory=bs_call

_utils:
	$(MAKE) --directory=utils

setup:
	@mkdir -p $(FOLDER_BIN) 

bs_call/src/Makefile.mk: bs_call/src/Makefile.mk.in bs_call/GEMTools/Makefile.mk.in bs_call/configure
	cd bs_call; ./configure

gem3: gem3-mapper/Makefile.mk
	$(MAKE) --directory=gem3-mapper
	
gem3-static: gem3-mapper/Makefile.mk
	$(MAKE) --directory=gem3-mapper static
	
gem3-debug: gem3-mapper/Makefile.mk
	$(MAKE) --directory=gem3-mapper debug
	
gem3-mapper/Makefile.mk: gem3-mapper/Makefile.mk.in gem3-mapper/configure
	cd gem3-mapper; ./configure

$(SAMTOOLS_DIR)/config.mk:
	cd $(SAMTOOLS_DIR); ./configure

$(FOLDER_BIN)/bcftools: $(BCFTOOLS_DIR) $(BCFTOOLS_DIR)/plugins/mextr.c $(BCFTOOLS_DIR)/plugins/snpxtr.c
	$(MAKE) --directory=$(BCFTOOLS_DIR) all

$(BCFTOOLS_DIR)/config.h:
	cd $(BCFTOOLS_DIR); ./configure
	touch $(BCFTOOLS_DIR)/config.h

$(SAMTOOLS_DIR):
	wget $(SAMTOOLS_URL) && tar -jxf $(SAMTOOLS_TAR) && rm -f $(SAMTOOLS_TAR)
	mv samtools-$(SAMTOOLS_VERSION) $(SAMTOOLS_DIR)

$(BCFTOOLS_DIR): 
	wget $(BCFTOOLS_URL) && tar -jxf $(BCFTOOLS_TAR) && rm -f $(BCFTOOLS_TAR)
	mv bcftools-$(BCFTOOLS_VERSION) $(BCFTOOLS_DIR)

$(BCFTOOLS_DIR)/plugins/%.c:
	ln -sf ../../gemBS_plugins/$(notdir $@) $(BCFTOOLS_DIR)/plugins/ 
	ln -sf ../../gemBS_plugins/$(basename $(notdir $@)).mk $(BCFTOOLS_DIR)/plugins/

$(FOLDER_BIN)/bedToBigBed:
	wget $(ARCHIVE)/bedToBigBed -O $(FOLDER_BIN)/bedToBigBed  && chmod 755 $(FOLDER_BIN)/bedToBigBed

$(FOLDER_BIN)/wigToBigWig:
	wget $(ARCHIVE)/wigToBigWig -O $(FOLDER_BIN)/wigToBigWig && chmod 755 $(FOLDER_BIN)/wigToBigWig

clean:
	@rm -f *~
	@rm -rf $(FOLDER_BIN) $(SAMTOOLS_DIR) $(BCFTOOLS_DIR)
	if [ -f "gem3-mapper/Makefile" ]; then $(MAKE) --directory=gem3-mapper clean; fi
	if [ -f "bs_call/Makefile" ]; then $(MAKE) --directory=bs_call clean; fi
