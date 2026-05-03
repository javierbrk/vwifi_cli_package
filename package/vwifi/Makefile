include $(TOPDIR)/rules.mk

PKG_NAME:=vwifi
PKG_VERSION:=7.0
PKG_RELEASE:=1

PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=4a9842e646c226a254df3300f1c98b86a947acd8
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/Raizo62/vwifi.git

PKG_MAINTAINER:=javier jorge <jjorge@inti.gob.ar>
PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/vwifi
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Virtual WiFi client for wireless device simulation
  URL:=https://github.com/Raizo62/vwifi
  DEPENDS:=+libnl +libstdcpp +kmod-mac80211-hwsim +libpthread
  PKGFLAGS:=nonshared
endef

define Package/vwifi/description
  Virtual WiFi client that allows simulation of wireless devices.
  This package provides the vwifi-client binary and configuration
  scripts for OpenWrt.
endef

define Package/vwifi/conffiles
/etc/config/vwifi
/etc/init.d/vwifi-client
endef

# Patch for struct msghdr
define Build/Prepare
	$(call Build/Prepare/Default)
endef

CMAKE_OPTIONS += \
	-DCMAKE_BUILD_TYPE=Release \
	-DCMAKE_CXX_COMPILER=$(TARGET_CXX) \
	-DCMAKE_C_FLAGS="$(TARGET_CFLAGS) $(TARGET_CPPFLAGS) -DDISABLE_VHOST" \
	-DCMAKE_CXX_FLAGS="$(TARGET_CXXFLAGS) $(TARGET_CPPFLAGS) -DDISABLE_VHOST" \
	-DCMAKE_EXE_LINKER_FLAGS="$(TARGET_LDFLAGS)"

define Package/vwifi/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/vwifi-client $(1)/usr/sbin/
	
	$(INSTALL_DIR) $(1)/etc/
	$(CP) ./files/* $(1)/
endef

define Package/vwifi/postinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	# Configure mac80211_hwsim module with 0 radios by default
	echo "Configuring mac80211_hwsim with 0 radios..."
	
	uci -q delete wireless.radio0 2>/dev/null || true
	uci -q delete wireless.default_radio0 2>/dev/null || true
	uci -q delete wireless.radio1 2>/dev/null || true
	uci -q delete wireless.default_radio1 2>/dev/null || true
	uci commit wireless 2>/dev/null || true
	
	/etc/init.d/vwifi-client enable
	
	echo "vwifi  installed."
	echo "Edit /etc/config/vwifi to update the server url."
	echo "then run: /etc/init.d/vwifi-client start"
fi
endef

define Package/vwifi/prerm
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	/etc/init.d/vwifi-client stop
	/etc/init.d/vwifi-client disable
fi
endef

$(eval $(call BuildPackage,vwifi))
