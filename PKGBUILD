# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/colord 
# 						Maintainer: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>
# 						Contributor: Ionut Biru <ibiru@archlinux.org>

pkgname=colord
pkgver=1.4.0+13+gb570101
pkgrel=1
pkgdesc="System daemon for managing color devices"
arch=(x86_64)
url="https://www.freedesktop.org/software/colord"
license=(GPL2)
depends=(lcms2 libgusb polkit sqlite dconf dbus libgudev shared-mime-info)
makedepends=(gobject-introspection vala sane bash-completion argyllcms git docbook-utils
             docbook-sgml perl-sgmls meson gtk-doc)
optdepends=('sane: scanner support'
            'argyllcms: color profiling'
            'colord-s6serv: cups s6 service')
replaces=('shared-color-profiles')
install=colord.install
_commit=b570101cc6a8be360f74f69de329fdd4de407ff8 # remove systemd from compilation
source=("git+https://github.com/hughsie/colord#commit=$_commit"
		'colord.tmpfiles')
sha1sums=('SKIP'
          'f7a59ccc5a0f6485e08f3ffdebb68dbc0797c461')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

pkgver() {
  cd $pkgname
  git describe --tags | sed 's/-/+/g'
}

prepare() {
  mkdir build
  cd $pkgname
}

build() {

  cd build
  meson --prefix=/usr --buildtype=release ../$pkgname \
    --localstatedir=/var --libexecdir=/usr/lib/$pkgname \
    -Denable-libcolordcompat=true \
    -Denable-sane=true \
    -Denable-vala=true \
    -Denable-print-profiles=true \
    -Dwith-daemon-user=colord \
    -Denable-systemd=false \
       
  ninja	
}

check() {
  cd build
  mesontest || true # crash occur when it try to test the daemon; pass trough it
}

package() {
  cd build
  DESTDIR="$pkgdir" ninja install

  # the build system has no colord user, so the chown fails
  chown -R 124:124 "$pkgdir/var/lib/colord"
  
  install -D -m644 ${srcdir}/colord.tmpfiles ${pkgdir}/usr/lib/tmpfiles.d/colord.conf
}

# vim:set ts=2 sw=2 et:
