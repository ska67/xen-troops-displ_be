# Maintainer: Stefan Kadow <arch at ska67 dot de>

pkgname=xen-troops-displ_be-git
pkgver=v0.2.1.r40.c244271
pkgrel=1
pkgdesc="Display and input backends for paravirtual Xen guest domains"
arch=('x86_64')
url='https://github.com/xen-troops/displ_be'
license=('GPL2')
groups=('xen-troops-pv_be')
depends=('xen' 'systemd-libs')
optdepends=('libdrm: for DRM mode'
            'wayland: for WAYLAND mode'
            'kmscon: virtual consoles')
makedepends=('git' 'cmake')
provides=("${pkgname%-git}" 'libxenbe.so')
conflicts=("${pkgname%-git}")
backup=('etc/conf.d/xen-troops-displ_be')
install=${pkgname%-git}.install
source=("${pkgname%-git}::git+https://github.com/xen-troops/displ_be.git"
	"git+https://github.com/xen-troops/libxenbe.git"
	'xen-troops-displ_be.conf'
	'xen-troops-displ_be.service'
	'xen-troops-displ_be.patch')
sha256sums=(
	'SKIP'
	'SKIP'
	'f9b3f361ff861d1589a215019a9d2d4248ad52a1447644fe94c27c5eacda84db' # xen-troops-displ_be.conf
	'c5fdd1ff96d8a4af406859d9d6bf9dcb103afd7f77669e6ab1d2201185827029' # xen-troops-displ_be.service
	'3a37b54529f8e70b383814024df515b8373ee55f6a1a4d35a107ccce9a3824e0' # xen-troops-displ_be.patch
)

prepare() {
	cd "${pkgname%-git}"
	patch --forward --strip=1 --input="${srcdir}/xen-troops-displ_be.patch"
}

pkgver() {
	cd "${srcdir}/${pkgname%-git}"
	printf "%s" "$(git describe --tags --long | sed 's/\([^-]*-\)g/r\1/;s/-/./g')"
}

build() {
	cd "libxenbe"
	mkdir build
	cd build
	cmake ../ \
		-DCMAKE_BUILD_TYPE=Release \
		-DCMAKE_INSTALL_PREFIX=/usr \
		-DWITH_DOC=OFF \
		-DWITH_TEST=OFF
	make
	cd "${srcdir}"

	cd "${pkgname%-git}"
	mkdir build
	cd build
	cmake ../ \
		-DCMAKE_BUILD_TYPE=Release \
		-DCMAKE_INSTALL_PREFIX=/usr \
		-DXENBE_LIB_PATH="${srcdir}/libxenbe/build/src/" \
		-DWITH_DOC=OFF \
		-DWITH_DRM=ON \
		-DWITH_ZCOPY=ON \
		-DWITH_WAYLAND=ON \
		-DWITH_IVI_EXTENSION=OFF \
		-DWITH_INPUT=ON
	make
	cd "${srcdir}"
}

package() {
	cd "libxenbe/build"
	make DESTDIR="${pkgdir}/" install
	cd "${srcdir}"

	cd "${pkgname%-git}/build"
	make DESTDIR="${pkgdir}/" install
	cd "${srcdir}"

	install -D -m 0644 xen-troops-displ_be.conf \
		"${pkgdir}/etc/conf.d/xen-troops-displ_be"
	install -D -m 0644 xen-troops-displ_be.service \
		"${pkgdir}/usr/lib/systemd/user/xen-troops-displ_be.service"
}
