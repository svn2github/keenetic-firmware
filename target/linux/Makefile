# 
# Copyright (C) 2006-2007 OpenWrt.org
# Copyright (C) 2010,2011 NDM Systems
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/target.mk

prereq clean download prepare compile install menuconfig oldconfig update refresh: FORCE
	TARGET_BUILD=1 $(MAKE) -C $(BOARD) $@

