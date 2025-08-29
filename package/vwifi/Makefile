include $(TOPDIR)/rules.mk

PKG_NAME:=vwifi
PKG_VERSION:=1.0.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/Raizo62/vwifi/archive/refs/heads/master.zip
PKG_HASH:=skip
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=master
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/Raizo62/vwifi.git

PKG_MAINTAINER:=OpenWrt Developer <developer@openwrt.org>
PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_DEPENDS:=libnl cmake/host
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
	-DCMAKE_CXX_FLAGS="$(TARGET_CXXFLAGS) $(TARGET_CPPFLAGS)" \
	-DCMAKE_EXE_LINKER_FLAGS="$(TARGET_LDFLAGS)"

define Package/vwifi/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/vwifi-client $(1)/usr/sbin/
	
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/etc/init.d/vwifi-client $(1)/etc/init.d/vwifi-client
	
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/etc/config/vwifi $(1)/etc/config/vwifi
	
	$(INSTALL_DIR) $(1)/lib/modules.d
	$(INSTALL_DATA) ./files/lib/modules.d/80-mac80211-hwsim $(1)/lib/modules.d/80-mac80211-hwsim
endef

define Package/vwifi/postinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	# Configurar módulo mac80211_hwsim con 0 radios por defecto
	echo "Configurando mac80211_hwsim..."
	
	# Eliminar configuraciones de radio existentes si existen
	uci -q delete wireless.radio0 2>/dev/null || true
	uci -q delete wireless.default_radio0 2>/dev/null || true
	uci -q delete wireless.radio1 2>/dev/null || true
	uci -q delete wireless.default_radio1 2>/dev/null || true
	uci commit wireless 2>/dev/null || true
	
	# Habilitar el servicio
	/etc/init.d/vwifi-client enable
	
	echo "vwifi instalado correctamente."
	echo "Edite /etc/config/vwifi para configurar la dirección del servidor."
	echo "Luego ejecute: /etc/init.d/vwifi-client start"
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
