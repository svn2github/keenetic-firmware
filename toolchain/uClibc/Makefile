#
# Copyright (C) 2006-2008 OpenWrt.org
# Copyright (C) 2010 ZyXEL.ru
# Copyright (C) 2010,2011 NDM Systems
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/target.mk

PKG_NAME:=uClibc
PKG_VERSION:=$(strip $(subst ",, $(CONFIG_UCLIBC_VERSION)))#"))
PKG_EXTRAVERSION:=$(strip $(subst ",, $(CONFIG_UCLIBC_EXTRA_VERSION)))#"))
PKG_VERSION_SNAPSHOT:=$(strip $(subst ",, $(CONFIG_UCLIBC_VERSION_SNAPSHOT)))#"))
ifeq ($(PKG_VERSION_SNAPSHOT),y)
PKG_SOURCE_URL:=http://www.uclibc.org/downloads/snapshots
else
PKG_SOURCE_URL:=http://www.uclibc.org/downloads
endif
PATCH_DIR:=./patches/$(PKG_VERSION)$(PKG_EXTRAVERSION)
CONFIG_DIR:=./config/$(PKG_VERSION)$(PKG_EXTRAVERSION)

ifeq ($(PKG_VERSION),0.9.28)
ifeq ($(PKG_EXTRAVERSION),.2)
PKG_MD5SUM:=959f25286e317f0d9e2103445c5a14c2
endif
ifeq ($(PKG_EXTRAVERSION),.3)
PKG_MD5SUM:=428405a36b4662980d9343b32089b5a6
endif
endif

ifeq ($(PKG_VERSION),0.9.29)
ifeq ($(PKG_EXTRAVERSION),)
# currently the default version, no special sub-directory needed
PATCH_DIR:=./patches
CONFIG_DIR:=./config
PKG_MD5SUM:=61dc55f43b17a38a074f347e74095b20
endif
endif

ifeq ($(PKG_VERSION),0.9.30.1)
PKG_MD5SUM:=1a4b84e5536ad8170563ffa88c34679c
endif
ifeq ($(PKG_VERSION),0.9.30.2)
PKG_MD5SUM:=e759ec855500082ac3e671dd6cacfdb0
endif
ifeq ($(PKG_VERSION),0.9.30.3)
PKG_MD5SUM:=73a4bf4a0fa508b01a7a3143574e3d21
endif
ifeq ($(PKG_VERSION),0.9.31)
PKG_MD5SUM:=52fb8a494758630c8d3ddd7f1e0daafd
endif

PKG_SOURCE:=uClibc-$(PKG_VERSION)$(PKG_EXTRAVERSION).tar.bz2
PKG_CAT:=bzcat

ifeq ($(PKG_VERSION),0.9.32)
PKG_SOURCE_URL:=git://git.busybox.net/uClibc
PKG_SOURCE_PROTO:=git
PKG_SOURCE_VERSION:=ef487a35f428da6240003dca23b9d29908e2285e
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION)-$(PKG_SOURCE_VERSION).tar.gz
PKG_CAT:=zcat
endif

STAGING_DIR_HOST:=$(TOOLCHAIN_DIR)
BUILD_DIR_HOST:=$(BUILD_DIR_TOOLCHAIN)
PKG_BUILD_DIR:=$(BUILD_DIR_HOST)/uClibc-$(PKG_VERSION)$(PKG_EXTRAVERSION)

include $(INCLUDE_DIR)/host-build.mk

STAMP_BUILT:=$(TOOLCHAIN_DIR)/stamp/.uclibc_installed
STAMP_INSTALLED:=$(TOOLCHAIN_DIR)/stamp/.uclibc-utils_installed

UCLIBC_TARGET_ARCH:=$(shell echo $(ARCH) | sed -e s'/-.*//' \
		-e 's/i.86/i386/' \
		-e 's/sparc.*/sparc/' \
		-e 's/arm.*/arm/g' \
		-e 's/avr32.*/avr32/g' \
		-e 's/m68k.*/m68k/' \
		-e 's/ppc/powerpc/g' \
		-e 's/v850.*/v850/g' \
		-e 's/sh64/sh/' \
		-e 's/sh[234].*/sh/' \
		-e 's/mips.*/mips/' \
		-e 's/mipsel.*/mips/' \
		-e 's/cris.*/cris/' \
)

define Build/Prepare/Snapshot
	$(PKG_UNPACK)
	rm -Rf $(PKG_BUILD_DIR)
	ln -snf $(BUILD_DIR_TOOLCHAIN)/uClibc $(PKG_BUILD_DIR)
	$(Build/Patch)
	$(if $(QUILT),touch $(PKG_BUILD_DIR)/.quilt_used)
endef

define Build/Prepare/V_0_9_28
	$(SED) 's,^KERNEL_SOURCE=.*,KERNEL_SOURCE=\"$(LINUX_HEADERS_DIR)\",g' \
		$(PKG_BUILD_DIR)/.config
ifeq ($(CONFIG_SOFT_FLOAT),y)
	$(SED) 's,.*HAS_FPU.*,HAS_FPU=n\nUCLIBC_HAS_FLOATS=y\nUCLIBC_HAS_SOFT_FLOAT=y,g' $(PKG_BUILD_DIR)/.config
endif
ifeq ($(CONFIG_C99_MATH),y)
	$(SED) 's,.*DO_C99_MATH.*,DO_C99_MATH=y,g' $(PKG_BUILD_DIR)/.config
endif
endef

define Build/Prepare
	@echo prepare uClibc version $(PKG_VERSION)$(PKG_EXTRAVERSION)  
ifeq ($(PKG_VERSION_SNAPSHOT),y)
	$(call Build/Prepare/Snapshot)
else
	$(call Build/Prepare/Default)
endif
	$(CP) ./$(CONFIG_DIR)/$(ARCH)$(if $(wildcard $(CONFIG_DIR)/$(ARCH).$(BOARD)),$(BOARD)) $(PKG_BUILD_DIR)/.config
ifeq ($(PKG_VERSION),0.9.28)
	$(call Build/Prepare/V_0_9_28)
endif
	$(SED) 's,^KERNEL_HEADERS=.*,KERNEL_HEADERS=\"$(LINUX_HEADERS_DIR)/include\",g' \
		-e 's,.*UCLIBC_HAS_FPU.*,UCLIBC_HAS_FPU=$(if $(CONFIG_SOFT_FLOAT),n\nUCLIBC_HAS_FLOATS=y\nUCLIBC_HAS_SOFT_FLOAT=y,y),g' \
		-e 's,^.*UCLIBC_HAS_LFS.*,UCLIBC_HAS_LFS=$(if $(CONFIG_LARGEFILE),y,n),g' \
		-e 's,^.*CONFIG_ARM_EABI.*,CONFIG_ARM_EABI=$(if $(CONFIG_EABI_SUPPORT),y,n),g' \
		-e 's,^.*CONFIG_ARM_OABI.*,CONFIG_ARM_OABI=$(if $(CONFIG_EABI_SUPPORT),n,y),g' \
		-e 's,^.*UCLIBC_HAS_SHADOW.*,UCLIBC_HAS_SHADOW=$(if $(CONFIG_SHADOW_PASSWORDS),y,n),g' \
		$(PKG_BUILD_DIR)/.config
	mkdir -p $(BUILD_DIR_HOST)/uClibc_dev/usr/include
	mkdir -p $(BUILD_DIR_HOST)/uClibc_dev/usr/lib
	mkdir -p $(BUILD_DIR_HOST)/uClibc_dev/lib
	if [ ! -f $(BUILD_DIR_HOST)/uClibc_dev/usr/include/linux/version.h ] ; then \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/asm $(BUILD_DIR_HOST)/uClibc_dev/usr/include/; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/asm-$(UCLIBC_TARGET_ARCH) $(BUILD_DIR_HOST)/uClibc_dev/usr/include/; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/asm-generic $(BUILD_DIR_HOST)/uClibc_dev/usr/include/; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/linux $(BUILD_DIR_HOST)/uClibc_dev/usr/include/; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/asm $(TOOLCHAIN_DIR)/include/ ; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/asm-$(UCLIBC_TARGET_ARCH) $(TOOLCHAIN_DIR)/include/ ; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/asm-generic $(TOOLCHAIN_DIR)/include/ ; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/linux $(TOOLCHAIN_DIR)/include/ ; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/asm $(PKG_BUILD_DIR)/include/ ; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/asm-$(UCLIBC_TARGET_ARCH) $(PKG_BUILD_DIR)/include/ ; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/asm-generic $(PKG_BUILD_DIR)/include/ ; \
		cp -pLR $(BUILD_DIR_TOOLCHAIN)/linux/include/linux $(PKG_BUILD_DIR)/include/ ; \
	fi;
ifeq ($(CONFIG_UCLIBC_NEW_METHOD_BUILD),y)
	$(MAKE) -C $(PKG_BUILD_DIR) oldconfig KBUILD_HAVE_NLS= HOSTCFLAGS="-DKBUILD_NO_NLS";
	$(MAKE) -C $(PKG_BUILD_DIR)/extra/config conf KBUILD_HAVE_NLS= HOSTCFLAGS="-DKBUILD_NO_NLS";
	PATH=$(TARGET_PATH) $(MAKE) -C $(PKG_BUILD_DIR) \
		PREFIX=$(BUILD_DIR_HOST)/uClibc_dev/ \
		DEVEL_PREFIX=/usr/ \
		RUNTIME_PREFIX=$(BUILD_DIR_HOST)/uClibc_dev/ \
		HOSTCC="$(HOSTCC)" \
		CC="$(TARGET_CC)" \
		ARCH="$(CONFIG_ARCH)" \
		CPU_CFLAGS="$(TARGET_CFLAGS)" \
		pregen install_headers;
else
	PATH=$(TARGET_PATH) $(MAKE) -C $(PKG_BUILD_DIR) \
		PREFIX=$(BUILD_DIR_HOST)/uClibc_dev/ \
		DEVEL_PREFIX=/usr/ \
		RUNTIME_PREFIX=$(BUILD_DIR_HOST)/uClibc_dev/ \
		HOSTCC="$(HOSTCC)" \
		CPU_CFLAGS="$(TARGET_CFLAGS)" \
		pregen install_dev;
endif
endef

define Build/Configure
endef

ifeq ($(CONFIG_UCLIBC_NEW_METHOD_BUILD),y)
UCLIBC_MAKE := PATH=$(TARGET_PATH) $(MAKE) -C $(PKG_BUILD_DIR) \
	$(TARGET_CONFIGURE_OPTS) \
	DEVEL_PREFIX=/ \
	RUNTIME_PREFIX=/ \
	HOSTCC="$(HOSTCC)" \
	ARCH="$(CONFIG_ARCH)" \
	CPU_CFLAGS="$(TARGET_CFLAGS)" \
	LIBGCC="$(subst libgcc.a,libgcc_initial.a,$(shell $(TARGET_CC) -print-libgcc-file-name))" \
	DOSTRIP=""
else
UCLIBC_MAKE := PATH=$(TARGET_PATH) $(MAKE) -C $(PKG_BUILD_DIR) \
	$(TARGET_CONFIGURE_OPTS) \
	DEVEL_PREFIX=/ \
	RUNTIME_PREFIX=/ \
	HOSTCC="$(HOSTCC)" \
	CPU_CFLAGS="$(TARGET_CFLAGS)"
endif

define Build/Compile
	$(SED) 's,^CROSS=.*,CROSS=$(TARGET_CROSS),g' $(PKG_BUILD_DIR)/Rules.mak
	$(UCLIBC_MAKE) PREFIX= all
	$(UCLIBC_MAKE) PREFIX=$(STAGING_DIR_HOST)/ install_runtime install_dev
	$(SED) 's,UCLIBC_VERSION=.*,UCLIBC_VERSION=$(PKG_VERSION),' $(TOOLCHAIN_DIR)/info.mk
	$(SED) 's,UCLIBC_PATCHVER=.*,UCLIBC_PATCHVER=$(PKG_EXTRAVERSION),' $(TOOLCHAIN_DIR)/info.mk
	rm -rf $(TOOLCHAIN_DIR)/lib/libc.so
	ln -s $(TOOLCHAIN_DIR)/lib/libc.so.0 $(TOOLCHAIN_DIR)/lib/libc.so
endef

define Build/Install
	$(UCLIBC_MAKE) PREFIX= utils
	$(INSTALL_DIR) $(TOOLCHAIN_DIR)/target-utils
	$(INSTALL_BIN) \
		$(PKG_BUILD_DIR)/utils/ldd \
		$(PKG_BUILD_DIR)/utils/ldconfig \
		$(TOOLCHAIN_DIR)/target-utils/
endef

define Build/Clean
	rm -rf $(PKG_BUILD_DIR) $(BUILD_DIR_HOST)/uClibc_dev
endef

$(eval $(call HostBuild))
