# 
# Copyright (C) 2007 OpenWrt.org
# Copyright (C) 2010,2011 NDM Systems
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk

PKG_NAME:=patch-cmdline

include $(INCLUDE_DIR)/host-build.mk

define Build/Compile
	$(HOSTCC) $(HOST_CFLAGS) -include endian.h -o $(PKG_BUILD_DIR)/$(PKG_NAME) src/$(PKG_NAME).c
endef

define Build/Install
	$(CP) $(PKG_BUILD_DIR)/patch-cmdline $(STAGING_DIR_HOST)/bin/
endef

define Build/Clean
	rm -f $(STAGING_DIR_HOST)/bin/patch-cmdline
endef

$(eval $(call HostBuild))
