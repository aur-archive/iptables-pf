# $Id: PKGBUILD 156635 2012-05-01 13:58:40Z allan $
# Maintainer: Oleksandr Natalenko aka post-factum <pfactum@gmail.com>
# Original maintainer: Ronald van Haren <ronald.archlinux.org>
# Contributor: Thomas Baechler <thomas@archlinux.org>

origname=iptables
pkgname=${origname}-pf
pkgver=1.4.13
pkgrel=4
pkgdesc='iptables using pf-kernel features: IMQ and layer7'
arch=('i686' 'x86_64')
license=('GPL2')
url='http://pf.natalenko.name/'
depends=('glibc' 'bash' 'l7-protocols')
makedepends=('linux-api-headers')
options=('!libtool')
conflicts=('iptables' 'iptables-l7')
provides=('iptables')
source=("http://www.iptables.org/projects/iptables/files/${origname}-${pkgver}.tar.bz2"
        iptables
        ip6tables
        empty.rules
        simple_firewall.rules
        iptables.conf.d
        empty-filter.rules
        empty-mangle.rules
        empty-nat.rules
        empty-raw.rules
        empty-security.rules
	iptables-1.4.12-IMQ-test4.diff
	libxt_layer7.c
	libxt_layer7.man
	xt_layer7.h)
backup=(etc/conf.d/iptables)
sha1sums=('bf1f1896e052d1813a7c96fa70f88be8dab3ff86'
          '5bb6fa526665cdd728c26f0f282f5a51f220cf88'
          '2db68906b603e5268736f48c8e251f3a49da1d75'
          '83b3363878e3660ce23b2ad325b53cbd6c796ecf'
          '9907f9e815592837abc7fa3264a401567b7606ab'
          'cdb830137192bbe002c6d01058656bd053ed0ddd'
          'd9f9f06b46b4187648e860afa0552335aafe3ce4'
          'c45b738b5ec4cfb11611b984c21a83b91a2d58f3'
          '1694d79b3e6e9d9d543f6a6e75fed06066c9a6c6'
          '7db53bb882f62f6c677cc8559cff83d8bae2ef73'
          'ebbd1424a1564fd45f455a81c61ce348f0a14c2e'
          '35d63629ab9e1ae2131b9beddfcf38b034ce840a'
          '2e30cc906ae2634c9d4434efbffa1b9d9d42f0f3'
          '2eed8ac2e914886d761f7315a22bbf75fcff78fd'
          '4f872337378e0f947a7ae730fa3eeafb7c7584cb')

build() {
  cd "${srcdir}/${origname}-${pkgver}"

  # add IMQ support
  patch -Np1 <${srcdir}/iptables-1.4.12-IMQ-test4.diff

  # add layer7 support
  cp ${srcdir}/libxt_layer7.{c,man} extensions/
  cp ${srcdir}/xt_layer7.h include/linux/netfilter/

  # http://bugs.archlinux.org/task/17046
  sed -i '87 i libxt_RATEEST.so: libxt_RATEEST.oo' extensions/GNUmakefile.in
  sed -i '88 i \\t${AM_VERBOSE_CCLD} ${CCLD} ${AM_LDFLAGS} -lm -shared ${LDFLAGS} -o $@ $<;\n' extensions/GNUmakefile.in

  # use system one
  rm include/linux/types.h

 ./configure --prefix=/usr \
     --libexecdir=/usr/lib/iptables --sysconfdir=/etc \
     --with-xtlibdir=/usr/lib/iptables \
     --enable-devel --enable-libipq \
     --enable-shared --enable-static 
# build fails when not enabling static, see if we can remove it on next build 
# 1.4.13 still fails

  make
}

package() {
  cd "${srcdir}/${origname}-${pkgver}"

  make DESTDIR="${pkgdir}" install

  cd "${srcdir}"
  install -D -m755 iptables "${pkgdir}"/etc/rc.d/iptables
  install -D -m755 ip6tables "${pkgdir}"/etc/rc.d/ip6tables
  install -D -m644 empty.rules "${pkgdir}"/etc/iptables/empty.rules
  install -D -m644 simple_firewall.rules "${pkgdir}"/etc/iptables/simple_firewall.rules
  install -D -m644 iptables.conf.d "${pkgdir}"/etc/conf.d/iptables

  mkdir -p "${pkgdir}"/var/lib/iptables
  install -m644 empty-{filter,mangle,nat,raw,security}.rules "${pkgdir}"/var/lib/iptables
}
