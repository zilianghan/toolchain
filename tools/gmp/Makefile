#
# Copyright (C) 2009 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk

PKG_NAME:=gmp
PKG_VERSION:=5.1.3

PKG_SOURCE_URL:=@GNU/gmp/
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_MD5SUM:=a082867cbca5e898371a97bb27b31fea

include $(INCLUDE_DIR)/host-build.mk

unexport CFLAGS

ifeq ($(HOST_OS),Darwin)
  GNU_HOST_NAME:=
  HOST_CONFIGURE_ARGS:=$(filter-out --target= --build= --host=,$(HOST_CONFIGURE_ARGS))
endif

HOST_CONFIGURE_ARGS += \
	--enable-static \
	--disable-shared \
	--enable-cxx \
	--enable-mpbsd

$(eval $(call HostBuild))
