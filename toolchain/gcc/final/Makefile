GCC_VARIANT:=final

include ../common.mk

GCC_CONFIGURE += \
	--enable-languages=$(TARGET_LANGUAGES) \
	--enable-shared \
	--enable-threads

ifneq ($(CONFIG_GCC_VERSION_4_6)$(CONFIG_GCC_VERSION_4_8),)
  GCC_CONFIGURE += \
	--enable-lto \
	--with-libelf=$(TOPDIR)/staging_dir/host
endif

ifneq ($(CONFIG_INSTALL_LIBSTDCPP),)
  GCC_CONFIGURE += \
	--enable-libstdcxx-time=yes
endif

define CleanupToolchain
	$(INSTALL_DIR) $(TOOLCHAIN_DIR)/$(REAL_GNU_TARGET_NAME)
	# Important!  Required for limits.h to be fixed.
	rm -rf $(TOOLCHAIN_DIR)/$(REAL_GNU_TARGET_NAME)/sys-include
	ln -sf ../include $(TOOLCHAIN_DIR)/$(REAL_GNU_TARGET_NAME)/sys-include
	rm -rf $(TOOLCHAIN_DIR)/$(REAL_GNU_TARGET_NAME)/lib
	ln -sf ../lib $(TOOLCHAIN_DIR)/$(REAL_GNU_TARGET_NAME)/lib
	$(if $(CONFIG_mips64)$(CONFIG_mips64el)$(CONFIG_x86_64),ln -sf ../lib64 $(TOOLCHAIN_DIR)/$(REAL_GNU_TARGET_NAME)/lib64)
endef

define Build/Configure
	$(CleanupToolchain)
	mkdir -p $(GCC_BUILD_DIR)
	(cd $(GCC_BUILD_DIR) && rm -f config.cache; \
		$(GCC_CONFIGURE) \
	);
endef

define Build/Compile
	$(GCC_MAKE) -C $(GCC_BUILD_DIR) all
endef

define SetupExtraArch
	for app in $(TOOLCHAIN_DIR)/bin/$(OPTIMIZE_FOR_CPU)*-{gcc,gcc-*,g++}; do \
		[ -e $$$$app ] || continue; \
		old_base=$$$$(basename $$$$app); \
		new_base=$(call qstrip,$(CONFIG_EXTRA_TARGET_ARCH_NAME))-$$$${old_base##$(OPTIMIZE_FOR_CPU)-}; \
		sed -e "s/@CC_BASE@/$$$$old_base/" \
			-e 's/@EXTRA_ARCH_OPTS@/$(call qstrip,$(CONFIG_EXTRA_TARGET_ARCH_OPTS))/' \
			 ../files/alternate-arch-cc.in > \
			 $(TOOLCHAIN_DIR)/bin/$$$$new_base; \
		chmod a+x $(TOOLCHAIN_DIR)/bin/$$$$new_base; \
	done
endef

define Build/Install
	$(CleanupToolchain)
	$(_SINGLE)$(GCC_MAKE) -C $(GCC_BUILD_DIR) install
	# Set up the symlinks to enable lying about target name.
	set -e; \
	(cd $(TOOLCHAIN_DIR); \
		ln -sf $(REAL_GNU_TARGET_NAME) $(GNU_TARGET_NAME); \
		cd bin; \
		for app in $(REAL_GNU_TARGET_NAME)-* ; do \
			ln -sf $$$${app} \
		   	$(GNU_TARGET_NAME)$$$${app##$(REAL_GNU_TARGET_NAME)}; \
		done; \
	);
	$(if $(CONFIG_EXTRA_TARGET_ARCH),$(call SetupExtraArch))
	$(RSTRIP) $(TOOLCHAIN_DIR)/libexec/gcc/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
	-$(RSTRIP) $(TOOLCHAIN_DIR)/libexec/gcc/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/install-tools
endef

$(eval $(call HostBuild))

