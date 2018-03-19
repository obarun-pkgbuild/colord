# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/colord 
# 						Maintainer: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>
# 						Contributor: Ionut Biru <ibiru@archlinux.org>

pkgname=colord
pkgver=1.4.2
pkgrel=2
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
options=(!emptydirs)
_commit=5b9aa8de432579a2636f13ad6895928f42511081 # tags/1.4.2^0
source=("git+https://github.com/hughsie/colord#commit=$_commit"
		'0001-Make-cd_color_get_blackbody_rgb_full-safer.patch'
		'colord.tmpfiles'
		'colord.sysusers')
sha1sums=('SKIP'
          '6b5012433df99a581c9c75866d67e7cc9e01dc5a'
          'f7a59ccc5a0f6485e08f3ffdebb68dbc0797c461'
          'e8dc15cc1bb3b5723d3b6e0f082e999b5c183863')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

pkgver() {
  cd $pkgname
  git describe --tags | sed 's/-/+/g'
}

prepare() {
  cd $pkgname
  patch -Np1 -i ../0001-Make-cd_color_get_blackbody_rgb_full-safer.patch
}

build() {

  arch-meson $pkgname build \
    -Denable-libcolordcompat=true \
    -Denable-sane=true \
    -Denable-vala=true \
    -Denable-print-profiles=true \
    -Dwith-daemon-user=colord \
    -Denable-systemd=false \
       
  ninja	-C build
}

check() {
  cd build
  meson test #|| true # crash occur when it try to test the daemon; pass trough it
}

package() {
  
  DESTDIR="$pkgdir" ninja -C build install

  
  install -D -m644 ${srcdir}/colord.tmpfiles ${pkgdir}/usr/lib/tmpfiles.d/colord.conf
  install -Dm644 "$srcdir/colord.sysusers" "$pkgdir/usr/lib/sysusers.d/colord.conf"
  
}

# vim:set ts=2 sw=2 et:
