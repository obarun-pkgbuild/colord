# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/colord 
# 						Maintainer: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>
# 						Contributor: Ionut Biru <ibiru@archlinux.org>

pkgname=colord
pkgver=1.3.5
pkgrel=2
pkgdesc="System daemon for managing color devices"
arch=(x86_64)
url="https://www.freedesktop.org/software/colord"
license=(GPL2)
depends=(lcms2 libgusb polkit sqlite dconf dbus eudev-obarun libgudev)
makedepends=(intltool gobject-introspection vala sane bash-completion argyllcms gnome-common git
             docbook-utils docbook-sgml perl-sgmls)
optdepends=('sane: scanner support'
            'argyllcms: color profiling'
            'cups-s6serv: cups s6 service'
            'cups-s6rcserv: cups s6-rc service'
            'cups-runitserv: cups runit service')
replaces=('shared-color-profiles')
install=colord.install
_commit=54723dc6e3373a63a43cf814ad7424932bd996f0 # tags/1.3.5^0
source=("git+https://github.com/hughsie/colord#commit=$_commit")
sha1sums=('SKIP')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

pkgver() {
  cd $pkgname
  git describe --tags | sed 's/-/+/g'
}

prepare() {


  cd $pkgname
  NOCONFIGURE=1 ./autogen.sh
}

build() {
  cd ${pkgname}
  ./configure 	--prefix=/usr \
				--sysconfdir=/etc \
				--localstatedir=/var \
				--libexecdir=/usr/lib/$pkgname \
				--with-systemdsystemunitdir=no \
				--with-daemon-user=colord \
				--enable-print-profiles \
				--enable-libcolordcompat \
				--enable-vala \
				--enable-sane \
				--enable-systemd_login=no \
				--enable-udev \
				--with-udevrulesdir=/etc/udev/rules.d/ \
				--disable-static
  sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool
  make
  PATH="$srcdir/path:$PATH" make
}

package() {
  cd ${pkgname}
  make DESTDIR="$pkgdir" install

  # the build system has no colord user, so the chown fails
  chown -R 124:124 "$pkgdir/var/lib/colord"
}

# vim:set ts=2 sw=2 et:
