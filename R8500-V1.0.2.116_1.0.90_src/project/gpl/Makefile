#
# Copyright 2005  Hon Hai Precision Ind. Co. Ltd.
#  All Rights Reserved.
# No portions of this material shall be reproduced in any form without the
# written permission of Hon Hai Precision Ind. Co. Ltd.
#
# All information contained in this document is Hon Hai Precision Ind.
# Co. Ltd. company private, proprietary, and trade secret property and
# are protected by international intellectual property laws and treaties.
#
# $Id$
#

include config.mk
include config.in

#
# Paths
#
#

SUBDIRS := 
SUBDIRS2 := minidlna
SUBDIRS3 := timemachine

SUBDIRS += curl
ifeq ($(CONFIG_TIMEMACHINE),y)
SUBDIRS += timemachine
endif
#SUBDIRS += dnsmasq
SUBDIRS += dnsmasq-2.15
SUBDIRS += udhcpd
#SUBDIRS += ppp/pppoecd
SUBDIRS += ppp-2.4.4/pppd pppoecd
SUBDIRS += ntpclient
SUBDIRS += bzip2

#SUBDIRS +=openssl
#SUBDIRS +=lzo-2.06
ifeq ($(CONFIG_OPENVPN),y)
SUBDIRS +=openvpn-2.3.1
#SUBDIRS +=GeoIP-1.5.1
endif
# allenwen add for openvpn

ifeq ($(CONFIG_TCPDUMP),y)
SUBDIRS +=libpcap-1.4.0
SUBDIRS +=tcpdump-4.4.0
endif

ifeq ($(CONFIG_RIP),y)
SUBDIRS	+= zebra
endif

ifeq ($(CONFIG_PPTP),y)
#SUBDIRS	+= ppp-2.4.4/pppd pptp
# SUBDIRS	+= ppp/pppd pptp
SUBDIRS	+= pptp
endif

ifeq ($(CONFIG_L2TP),y)
SUBDIRS += l2tpd-0.69
endif

#ifeq ($(CONFIG_BPA),y)
#SUBDIRS	+= bpalogin
#endif

ifeq ($(CONFIG_DLNA),y)
SUBDIRS += minidlna
endif

ifeq ($(CONFIG_ISERVER),y)
SUBDIRS += iserver
endif


SUBDIRS += zeroconf-0.9

ifeq ($(IPERF_ENABLE_FLAG),y)
SUBDIRS	+= iperf-2.0.5
endif

#water, 10/30/2008, @samba
ifeq ($(SAMBA_ENABLE_FLAG),y)
#SUBDIRS	+= samba-3.0.13
SUBDIRS	+= samba4
SUBDIRS	+= bftpd-1.6.6
SUBDIRS	+= hdparm-9.43
SUBDIRS	+= hd-idle-1.04
endif

# pling added 05/04/2009, for NTFS-3g
ifeq ($(CONFIG_NTFS_3G),y)
SUBDIRS += ntfs-3g-2009.3.8
endif
#pling added 05/07/2009, for mtools (to read FAT volume label)
ifeq ($(CONFIG_MTOOLS),y)
SUBDIRS += mtools-4.0.10
endif
#Foxconn add start, stanley, 01/16/2010 @IGMP
ifeq ($(CONFIG_IGMP_PROXY),y)
SUBDIRS	+= IGMP-PROXY
endif
#Foxconn add end, stanley, 01/16/2010 @IGMP
SUBDIRS += iproute2

# Foxconn add start by Hank for ecosystem support 08/14/2012 
ifeq ($(CONFIG_DOWNLOADER),y)
SUBDIRS +=transmission
SUBDIRS +=amule
endif
ifeq ($(CONFIG_WGET),y)
OPENSSL_DIR = $(GPLTOPDIR)/openssl
WGET_DIR = wget-1.12
SUBDIRS += $(WGET_DIR)
endif
# Foxconn add start, Snoopy.wu, KWILT support, 06/01/2015
ifeq ($(CONFIG_KWILT),y)
SUBDIRS += netkit-ftp-0.18-pre1
endif
# Foxconn add end, Snoopy.wu, 06/01/2015


# Foxconn Perry added start, for zip and unzip 2013/05/09
SUBDIRS += zip30 unzip60


all: ECOSYSTEM config dhcp6c dhcp6s
	for i in ${SUBDIRS}; do \
		(cd $$i; make) || exit 1; \
	done

tm: 
	for i in ${SUBDIRS3}; do \
		(cd $$i; make ; make install) || exit 1; \
	done

dlna: 
	for i in ${SUBDIRS2}; do \
		(cd $$i; make) || exit 1; \
	done

ECOSYSTEM:
ifeq ($(CONFIG_WGET),y)
	mkdir -p $(OPENSSL_DIR)/lib && rm -rf $(OPENSSL_DIR)/lib/libssl.so.1.0.0 && cp $(OPENSSL_DIR)/../../../src/router/prebuilt/target/lib/libssl.so.1.0.0 $(OPENSSL_DIR)/lib/
	cd $(WGET_DIR) && $(CONFIGURE) CFLAGS="$(CFLAGS) -DMB_CUR_MAX=1" LDFLAGS="$(LDFLAGS) -L$(OPENSSL_DIR)" \
		--with-ssl=openssl --with-libssl-prefix=$(OPENSSL_DIR) --prefix=$(TARGETDIR)
endif
	# Foxconn add end by Hank for ecosystem support 08/14/2012	

config: GeoIP_config openvpn_config tcpdump_config iperf_config

libpcap_config:
	if [ ! -f ./libpcap-1.4.0/Makefile ];then \
        cd libpcap-1.4.0 && (CC=$(CROSS_COMPILE)gcc CFLAGS="$(CFLAGS)" \
	$(CONFIGURE) --with-pcap=linux --prefix=$(SRCBASE)/../ap/gpl/libpcap-1.4.0/lib); \
        fi

libpcap_make: libpcap_config
	cd libpcap-1.4.0 && make install

tcpdump_config: libpcap_make
	if [ ! -f ./tcpdump-4.4.0/Makefile ];then \
        cd tcpdump-4.4.0 && (CC=$(CROSS_COMPILE)gcc CFLAGS="$(CFLAGS)" \
        $(CONFIGURE) ac_cv_linux_vers=2 --prefix=$(TARGETDIR)/usr); \
        fi

GeoIP_config:
	if [ ! -f ./GeoIP-1.5.1/Makefile ];then \
	cd GeoIP-1.5.1 && (CC=$(CROSS_COMPILE)gcc \
	ac_cv_func_malloc_0_nonnull=yes ac_cv_func_realloc_0_nonnull=yes CFLAGS="$(CFLAGS)" \
	$(CONFIGURE) --with-gnu-ld --prefix=$(TARGETDIR)/usr); \
	fi

lzo_config:
	if [ ! -f ./lzo-2.06/Makefile ];then \
	cd lzo-2.06 && (CC=$(CROSS_COMPILE)gcc CFLAGS="$(CFLAGS)" \
	$(CONFIGURE) --prefix=$(TARGETDIR)/usr/local); \
	fi

lzo_make: lzo_config
	cd lzo-2.06 && make install

openvpn_config: lzo_make
	if [ ! -f ./openvpn-2.3.1/Makefile ];then \
	cd openvpn-2.3.1 && (CC=$(CROSS_COMPILE)gcc LZO_CFLAGS=-I$(TARGETDIR)/usr/local/include \
	LZO_LIBS="-L$(TARGETDIR)/usr/local/lib -llzo2" \
	CFLAGS="$(CFLAGS) -I$(SRCBASE)/../ap/acos/include" \
	LDFLAGS="-L$(ROUTERDIR)/nvram -L$(INSTALLDIR)/nvram/usr/lib -lnvram" \
	$(CONFIGURE) --prefix=$(TARGETDIR)/usr/local --disable-pam-dlopen \
	--disable-plugin-auth-pam); \
	fi 

iperf_config:
	if [ ! -f ./iperf-2.0.5/Makefile ];then \
	cd iperf-2.0.5 && (CC=$(CROSS_COMPILE)gcc \
	CFLAGS="$(CFLAGS) -I$(SRCBASE)/../ap/acos/include" \
	LDFLAGS="-L$(ROUTERDIR)/nvram -L$(INSTALLDIR)/nvram/usr/lib -lnvram" \
	$(CONFIGURE) --prefix=$(TARGETDIR)/usr --disable-pam-dlopen \
	--disable-plugin-auth-pam); \
	fi
 
dhcp6s dhcp6c:
	[ ! -d dhcp6 ] || [ -f dhcp6/Makefile ] || ( cd dhcp6 &&(CC=gcc CFLAGS+="-I../../../src/router/shared" ./configure)&& cd .. )
	[ ! -d dhcp6 ] || $(MAKE) -C dhcp6 CC=$(CROSS_COMPILE)gcc LIBS="-L ../../../src/router/lib" $@

dhcp6s-install:
	[ ! -d dhcp6 ] || install -D -m 755 dhcp6/dhcp6s $(TARGETDIR)/usr/sbin/dhcp6s
	[ ! -d dhcp6 ] || $(STRIP) $(TARGETDIR)/usr/sbin/dhcp6s

dhcp6c-install:
	[ ! -d dhcp6 ] || install -D -m 755 dhcp6/dhcp6c $(TARGETDIR)/usr/sbin/dhcp6c
	[ ! -d dhcp6 ] || $(STRIP) $(TARGETDIR)/usr/sbin/dhcp6c

dhcp6s-clean dhcp6c-clean:
	[ ! -f dhcp6/Makefile ] || $(MAKE) -C dhcp6 distclean

install: dhcp6s-install dhcp6c-install
	for i in ${SUBDIRS}; do \
		(cd $$i; make install) || exit 1; \
	done
	
clean: dhcp6c-clean
	for i in ${SUBDIRS}; do \
		(cd $$i; make clean); \
	done

.PHONY: $(SUBDIRS)
