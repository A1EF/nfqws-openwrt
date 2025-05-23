include $(TOPDIR)/rules.mk

PKG_NAME:=nfqws
PKG_VERSION:=_VERSION_
PKG_RELEASE:=1

PKG_SOURCE:=v$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/bol-van/zapret/archive/refs/tags/
PKG_HASH:=skip

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=docs/LICENSE.txt

PKG_BUILD_DIR:=$(BUILD_DIR)/zapret-$(PKG_VERSION)
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

MAKE_PATH:=nfq

define Package/$(PKG_NAME)
  SECTION:=net
  CATEGORY:=Network
  TITLE:=$(PKG_NAME)
  SUBMENU:=Zapret
  URL:=https://github.com/bol-van/zapret
  PKG_BUILD_DEPENDS:=libcap libnetfilter-queue zlib
  EXTRA_DEPENDS:=kmod-nft-queue
endef

define Package/$(PKG_NAME)/description
 DPI bypass packet modifier and a NFQUEUE queue handler.
endef

define Build/Prepare
	$(Build/Prepare/Default)
	sed -i 's/\(CFLAGS +=.*\)/\1 -static/' $(PKG_BUILD_DIR)/$(MAKE_PATH)/Makefile
	rm -f $(PKG_BUILD_DIR)/$(MAKE_PATH)/nfqws
endef

define Package/$(PKG_NAME)/conffiles
/etc/config/zapret
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/$(MAKE_PATH)/nfqws $(1)/usr/sbin
	$(INSTALL_DIR) $(1)/etc/config/zapret
	$(INSTALL_CONF) files/nfqws.cfg $(1)/etc/config/zapret
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) files/nfqws $(1)/etc/init.d
endef

define Package/$(PKG_NAME)/preinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	if [ "$${PKG_UPGRADE}" = "1" ]; then
		if [ -x "/etc/init.d/nfqws" ]; then
			pgrep nfqws >/dev/null && /etc/init.d/nfqws stop
		fi
	fi
fi
exit 0
endef

define Package/$(PKG_NAME)/postinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	/etc/init.d/nfqws enable
	pgrep nfqws >/dev/null || /etc/init.d/nfqws start
fi
exit 0
endef

define Package/$(PKG_NAME)/prerm
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	/etc/init.d/nfqws disable
	pgrep nfqws >/dev/null && /etc/init.d/nfqws stop
fi
exit 0
endef

define Package/$(PKG_NAME)/postrm
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	[ -f "/etc/init.d/nfqws-opkg" ] && rm -f /etc/init.d/nfqws-opkg
	[ -f "/etc/config/zapret/nfqws.cfg-opkg" ] && rm -f /etc/config/zapret/nfqws.cfg-opkg
fi
exit 0
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
