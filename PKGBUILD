# Maintainer: Dennis Fink <the_metalgamer@hackerspace.lu>
#
# Heavily based on the thttpd PKGBUILD

pkgname=sthttpd-git
pkgver=2.27.6.g7f9eabd
pkgrel=2
pkgdesc='Simple, small, portable, fast, and secure HTTP server'
url='http://opensource.dyc.edu/sthttpd'
license=('custom:BSD')
arch=('i686' 'x86_64' 'armv7h' 'armv6h')
makedepends=('git')
backup=('etc/thttpd.conf')
source=('git://opensource.dyc.edu/sthttpd.git'
        'logrotate.d'
        'service'
        'config'
        'rc.d')
sha1sums=('SKIP'
          '36ffeefd1675ca4920605b1b5ca32dd5141a8f23'
          '7c36b80293377795d80245df96029ae7d2ab7658'
          '16640870a69cfc48021ee3acfea7c95834549d46'
          '5f0e499ecd3371f7f495e4c751ccfcbfdcd20e14')
provides=('sthttpd')
conflicts=('sthttpd' 'thttpd')

_gitname=sthttpd

pkgver() {
    cd "$srcdir"/$_gitname
    git describe --always | sed 's|-|.|g' | sed 's|begin\.||g'
}

build() {

    cd "$srcdir"/$_gitname

    ./autogen.sh
	./configure --prefix=/usr --mandir=/usr/share/man
	sed "s/^CFLAGS =/CFLAGS = ${CFLAGS} /" -i Makefile */Makefile
	make
}

package() {
	cd "${srcdir}/${_gitname}"

	install -d "${pkgdir}"/usr/{bin,share/man/man{1,8}}
	make \
		bindir="${pkgdir}"/usr/bin \
		sbindir="${pkgdir}"/usr/bin \
		WEBDIR="${pkgdir}"/srv/http \
		mandir="${pkgdir}"/usr/share/man \
		WEBGROUP=root install

	rm -fr "${pkgdir}"/srv
	chown root:root -R "${pkgdir}"
	chmod 755 -R "${pkgdir}"/usr/bin # strip needs u+w

	install -Dm755 ../rc.d "${pkgdir}"/etc/rc.d/thttpd
	install -Dm644 ../config "${pkgdir}"/etc/thttpd.conf
	install -Dm644 ../service "${pkgdir}"/usr/lib/systemd/system/thttpd.service
	install -Dm644 ../logrotate.d "${pkgdir}"/etc/logrotate.d/thttpd

	install -d "${pkgdir}"/{usr/share/licenses/"${pkgname}",var/log/thttpd}
    head -n 26 src/thttpd.c > "${pkgdir}"/usr/share/licenses/"${pkgname}"/LICENSE
}
