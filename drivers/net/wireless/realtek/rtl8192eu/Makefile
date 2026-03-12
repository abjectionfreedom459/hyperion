# SPDX-License-Identifier: GPL-2.0
# Makefile for Realtek RTL8192EU USB 802.11n driver
# Hyperion Kernel Project – in-tree port of the Realtek vendor driver.
#
# Architecture notes:
#   This driver uses the Realtek private wireless stack (wext/cfg80211 wrapper).
#   It does NOT use mac80211.  This is intentional – a proper mac80211 port
#   would require porting ~20,000 lines of chip HAL from scratch and does not
#   exist anywhere publicly as of 2026.  The cfg80211 path used here is the
#   only architecture that correctly programs the RTL8192EU hardware and gives
#   working internet.

# ---- fixed chip/HCI choices for this in-tree driver ----
CONFIG_RTL8192E  := y
CONFIG_USB_HCI   := y

MODULE_NAME      := 8192eu

# ---- feature knobs (edit to taste) ----
CONFIG_POWER_SAVING         := y
CONFIG_USB_AUTOSUSPEND      := n
CONFIG_BT_COEXIST           := y
CONFIG_MP_INCLUDED          := y
CONFIG_80211W               := y
CONFIG_EFUSE_CONFIG_FILE    := y
CONFIG_LOAD_PHY_PARA_FROM_FILE := y
CONFIG_TXPWR_BY_RATE        := y
CONFIG_TXPWR_BY_RATE_EN     := n
CONFIG_TXPWR_LIMIT          := y
CONFIG_TXPWR_LIMIT_EN       := n
CONFIG_RTW_CHPLAN           := 0xFF
CONFIG_RTW_ADAPTIVITY_EN    := disable
CONFIG_RTW_ADAPTIVITY_MODE  := normal
CONFIG_BR_EXT               := y
CONFIG_WIFI_MONITOR         := y
CONFIG_RTW_NAPI             := y
CONFIG_RTW_GRO              := n
CONFIG_RTW_NETIF_SG         := y
CONFIG_RTW_DEBUG            := n
CONFIG_RTW_LOG_LEVEL        := 2

# ---- compiler flags ----
ccflags-y += -O2
ccflags-y += -Wno-unused-variable -Wno-unused-label -Wno-unused-function

ccflags-y += -I$(src)/include
ccflags-y += -I$(src)/hal/phydm
ccflags-y += -I$(src)/platform
ccflags-y += -I$(src)/hal/btc

ccflags-y += -DCONFIG_RTL8192E
ccflags-y += -DCONFIG_USB_HCI
ccflags-y += -DCONFIG_LITTLE_ENDIAN
# cfg80211 glue – required for NetworkManager / wpa_supplicant to see the adapter
ccflags-y += -DCONFIG_IOCTL_CFG80211
ccflags-y += -DRTW_USE_CFG80211_STA_EVENT
ccflags-y += -DDM_ODM_SUPPORT_TYPE=0x04

ifeq ($(CONFIG_POWER_SAVING), y)
ccflags-y += -DCONFIG_POWER_SAVING
endif

ifeq ($(CONFIG_USB_AUTOSUSPEND), y)
ccflags-y += -DCONFIG_USB_AUTOSUSPEND
endif

ifeq ($(CONFIG_BT_COEXIST), y)
ccflags-y += -DCONFIG_BT_COEXIST
endif

ifeq ($(CONFIG_MP_INCLUDED), y)
ccflags-y += -DCONFIG_MP_INCLUDED
endif

ifeq ($(CONFIG_80211W), y)
ccflags-y += -DCONFIG_IEEE80211W
endif

ifeq ($(CONFIG_EFUSE_CONFIG_FILE), y)
ccflags-y += -DCONFIG_EFUSE_CONFIG_FILE
ccflags-y += -DEFUSE_MAP_PATH=\"/system/etc/wifi/wifi_efuse_8192eu.map\"
ccflags-y += -DWIFIMAC_PATH=\"/data/wifimac.txt\"
endif

ifeq ($(CONFIG_LOAD_PHY_PARA_FROM_FILE), y)
ccflags-y += -DCONFIG_LOAD_PHY_PARA_FROM_FILE
ccflags-y += -DREALTEK_CONFIG_PATH=\"/lib/firmware/\"
endif

ifeq ($(CONFIG_TXPWR_BY_RATE), y)
ccflags-y += -DCONFIG_TXPWR_BY_RATE=1
else
ccflags-y += -DCONFIG_TXPWR_BY_RATE=0
endif

ifeq ($(CONFIG_TXPWR_BY_RATE_EN), y)
ccflags-y += -DCONFIG_TXPWR_BY_RATE_EN=1
else
ccflags-y += -DCONFIG_TXPWR_BY_RATE_EN=0
endif

ifeq ($(CONFIG_TXPWR_LIMIT), y)
ccflags-y += -DCONFIG_TXPWR_LIMIT=1
else
ccflags-y += -DCONFIG_TXPWR_LIMIT=0
endif

ifeq ($(CONFIG_TXPWR_LIMIT_EN), y)
ccflags-y += -DCONFIG_TXPWR_LIMIT_EN=1
else
ccflags-y += -DCONFIG_TXPWR_LIMIT_EN=0
endif

ifeq ($(CONFIG_RTW_ADAPTIVITY_EN), disable)
ccflags-y += -DCONFIG_RTW_ADAPTIVITY_EN=0
else ifeq ($(CONFIG_RTW_ADAPTIVITY_EN), enable)
ccflags-y += -DCONFIG_RTW_ADAPTIVITY_EN=1
endif

ifeq ($(CONFIG_RTW_ADAPTIVITY_MODE), normal)
ccflags-y += -DCONFIG_RTW_ADAPTIVITY_MODE=0
else ifeq ($(CONFIG_RTW_ADAPTIVITY_MODE), carrier_sense)
ccflags-y += -DCONFIG_RTW_ADAPTIVITY_MODE=1
endif

ifeq ($(CONFIG_BR_EXT), y)
ccflags-y += -DCONFIG_BR_EXT
ccflags-y += '-DCONFIG_BR_EXT_BRNAME="br0"'
endif

ifeq ($(CONFIG_WIFI_MONITOR), y)
ccflags-y += -DCONFIG_WIFI_MONITOR
endif

ifeq ($(CONFIG_RTW_NAPI), y)
ccflags-y += -DCONFIG_RTW_NAPI
endif

ifeq ($(CONFIG_RTW_GRO), y)
ccflags-y += -DCONFIG_RTW_GRO
endif

ifeq ($(CONFIG_RTW_NETIF_SG), y)
ccflags-y += -DCONFIG_RTW_NETIF_SG
endif

ifeq ($(CONFIG_RTW_DEBUG), y)
ccflags-y += -DCONFIG_RTW_DEBUG
ccflags-y += -DRTW_LOG_LEVEL=$(CONFIG_RTW_LOG_LEVEL)
endif

# ---- phydm ----
include $(src)/hal/phydm/phydm.mk

# ---- core ----
rtk_core := \
	core/rtw_cmd.o \
	core/rtw_security.o \
	core/rtw_debug.o \
	core/rtw_io.o \
	core/rtw_ioctl_set.o \
	core/rtw_ieee80211.o \
	core/rtw_mlme.o \
	core/rtw_mlme_ext.o \
	core/rtw_mi.o \
	core/rtw_wlan_util.o \
	core/rtw_vht.o \
	core/rtw_pwrctrl.o \
	core/rtw_rf.o \
	core/rtw_chplan.o \
	core/rtw_recv.o \
	core/rtw_sta_mgt.o \
	core/rtw_ap.o \
	core/mesh/rtw_mesh.o \
	core/mesh/rtw_mesh_pathtbl.o \
	core/mesh/rtw_mesh_hwmp.o \
	core/rtw_xmit.o \
	core/rtw_p2p.o \
	core/rtw_rson.o \
	core/rtw_tdls.o \
	core/rtw_br_ext.o \
	core/rtw_iol.o \
	core/rtw_sreset.o \
	core/rtw_btcoex_wifionly.o \
	core/rtw_btcoex.o \
	core/rtw_beamforming.o \
	core/rtw_odm.o \
	core/rtw_rm.o \
	core/rtw_rm_fsm.o \
	core/efuse/rtw_efuse.o

$(MODULE_NAME)-y += $(rtk_core)
$(MODULE_NAME)-$(CONFIG_MP_INCLUDED) += core/rtw_mp.o

# ---- OS / USB interface ----
$(MODULE_NAME)-y += \
	os_dep/osdep_service.o \
	os_dep/linux/os_intfs.o \
	os_dep/linux/usb_intf.o \
	os_dep/linux/usb_ops_linux.o \
	os_dep/linux/ioctl_linux.o \
	os_dep/linux/xmit_linux.o \
	os_dep/linux/mlme_linux.o \
	os_dep/linux/recv_linux.o \
	os_dep/linux/ioctl_cfg80211.o \
	os_dep/linux/rtw_cfgvendor.o \
	os_dep/linux/wifi_regd.o \
	os_dep/linux/rtw_android.o \
	os_dep/linux/rtw_proc.o \
	os_dep/linux/rtw_rhashtable.o

$(MODULE_NAME)-$(CONFIG_MP_INCLUDED) += os_dep/linux/ioctl_mp.o

# ---- HAL common ----
$(MODULE_NAME)-y += \
	hal/hal_intf.o \
	hal/hal_com.o \
	hal/hal_com_phycfg.o \
	hal/hal_phy.o \
	hal/hal_dm.o \
	hal/hal_dm_acs.o \
	hal/hal_btcoex_wifionly.o \
	hal/hal_btcoex.o \
	hal/hal_mp.o \
	hal/hal_mcc.o \
	hal/hal_hci/hal_usb.o \
	hal/led/hal_led.o \
	hal/led/hal_usb_led.o

# ---- HAL RTL8192E + USB ----
$(MODULE_NAME)-y += \
	hal/HalPwrSeqCmd.o \
	hal/rtl8192e/Hal8192EPwrSeq.o \
	hal/rtl8192e/rtl8192e_xmit.o \
	hal/rtl8192e/rtl8192e_sreset.o \
	hal/rtl8192e/rtl8192e_hal_init.o \
	hal/rtl8192e/rtl8192e_phycfg.o \
	hal/rtl8192e/rtl8192e_rf6052.o \
	hal/rtl8192e/rtl8192e_dm.o \
	hal/rtl8192e/rtl8192e_rxdesc.o \
	hal/rtl8192e/rtl8192e_cmd.o \
	hal/rtl8192e/hal8192e_fw.o \
	hal/rtl8192e/usb/usb_halinit.o \
	hal/rtl8192e/usb/rtl8192eu_led.o \
	hal/rtl8192e/usb/rtl8192eu_xmit.o \
	hal/rtl8192e/usb/rtl8192eu_recv.o \
	hal/rtl8192e/usb/usb_ops_linux.o \
	hal/efuse/rtl8192e/HalEfuseMask8192E_USB.o

# ---- BT coexistence for 8192e ----
ifeq ($(CONFIG_BT_COEXIST), y)
$(MODULE_NAME)-y += \
	hal/btc/halbtc8192e1ant.o \
	hal/btc/halbtc8192e2ant.o
endif

# ---- phydm ----
$(MODULE_NAME)-y += $(_PHYDM_FILES)

# ---- platform ----
$(MODULE_NAME)-y += platform/platform_ops.o

# ---- wire it all together ----
obj-$(CONFIG_RTL8192EU) := $(MODULE_NAME).o

EXTRA_CFLAGS += $(ccflags-y)
EXTRA_LDFLAGS += $(ldflags-y)
