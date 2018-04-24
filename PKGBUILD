# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/colord 
# 						Maintainer: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>
# 						Contributor: Ionut Biru <ibiru@archlinux.org>

pkgbase=colord
pkgname=(colord colord-sane)
pkgver=1.4.3
pkgrel=2
pkgdesc="System daemon for managing color devices"
arch=(x86_64)
url="https://www.freedesktop.org/software/colord"
license=(GPL2)
depends=(lcms2 libgusb polkit sqlite dconf dbus libgudev shared-mime-info)
makedepends=(gobject-introspection vala sane bash-completion argyllcms git docbook-utils
             docbook-sgml perl-sgmls meson gtk-doc)
optdepends=('sane: scanner support'
            'argyllcms: color profiling')
options=(!emptydirs)
_commit=cd7faac0d81f2dabef6c9787baf78b9e8fb0e1ae # tags/1.4.3^0
source=("git+https://github.com/hughsie/colord#commit=$_commit"
		'colord.tmpfiles'
		'colord.sysusers')
sha1sums=('SKIP'
          '6b5012433df99a581c9c75866d67e7cc9e01dc5a'
          'f7a59ccc5a0f6485e08f3ffdebb68dbc0797c461'
          'e8dc15cc1bb3b5723d3b6e0f082e999b5c183863')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

pkgver() {
  cd $pkgbase
  git describe --tags | sed 's/-/+/g'
}

#prepare() {
#  cd $pkgbasepatch -Np1 -i ../0001-Make-cd_color_get_blackbody_rgb_full-safer.patch
#}

build() {

  arch-meson $pkgbase build \
    -D libcolordcompat=true \
    -D sane=true \
    -D vapi=true \
    -D print-profiles=true \
    -D daemon-user=colord \
    -D systemd=false 
       
  ninja	-C build
}

check() {
  cd build
  meson test #|| true # crash occur when it try to test the daemon; pass trough it
}

package_colord() {

	optdepends=('argyllcms: color profiling'
				'colord-sane: SANE support')
  replaces=(shared-color-profiles)

  DESTDIR="$pkgdir" ninja -C build install

  
  install -D -m644 ${srcdir}/colord.tmpfiles ${pkgdir}/usr/lib/tmpfiles.d/colord.conf
  install -Dm644 "$srcdir/colord.sysusers" "$pkgdir/usr/lib/sysusers.d/colord.conf"
  
  ## Split colord-sane
  mkdir -p colord-sane/usr/lib/colord-plugins
  mv {"$pkgdir",colord-sane}/usr/lib/colord-sane
  mv {"$pkgdir",colord-sane}/usr/lib/colord-plugins/libcolord_sensor_sane.so
}

package_colord-sane(){
  pkgdesc+=" (SANE support)"
  depends=("colord=$pkgver-$pkgrel" sane)
  mv colord-sane/* "$pkgdir"
}
