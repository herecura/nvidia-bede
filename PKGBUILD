# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=304.43
_extramodules=3.5-BEDE-external
pkgrel=1
pkgdesc="NVIDIA drivers for linux-bede"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
makedepends=('linux-bede>=3.5' 'linux-bede<3.6' 'linux-bede-headers>=3.5' 'linux-bede-headers<3.6' "nvidia-utils=$pkgver")
conflicts=('nvidia-96xx' 'nvidia-173xx')
replaces=('nvidia-bemm')
license=('custom')
install=nvidia.install
options=(!strip)

if [ "$CARCH" = "i686" ]; then
	_arch='x86'
	_pkg="NVIDIA-Linux-$_arch-$pkgver"
	source=("http://download.nvidia.com/XFree86/Linux-$_arch/$pkgver/$_pkg.run")
	md5sums=('393260b7e8d8195e982eb718f5014c3d')
elif [ "$CARCH" = "x86_64" ]; then
	_arch='x86_64'
	_pkg="NVIDIA-Linux-$_arch-$pkgver-no-compat32"
	source=("http://download.nvidia.com/XFree86/Linux-$_arch/$pkgver/$_pkg.run")
	md5sums=('e634de31c96f7b418ed7fdc385ab049d')
fi

build() {
	_kernver="$(cat /usr/lib/modules/$_extramodules/version)"
	cd "$srcdir"
	sh $_pkg.run --extract-only
	cd $_pkg/kernel
	sed -e '/CFLAGS="$CFLAGS/s:-I$SOURCES/arch/x86/include:& -I$OUTPUT/arch/x86/include/generated:' -i conftest.sh
 	make SYSSRC=/usr/lib/modules/$_kernver/build module
}

package() {
	depends=('linux-bede>=3.5' 'linux-bede<3.6' "nvidia-utils=${pkgver}")

	install -Dm644 "$srcdir/$_pkg/kernel/nvidia.ko" \
		"$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia.ko"

	install -dm755 "$pkgdir/usr/lib/modprobe.d"
	echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
	echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

	# gzip all modules
	find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;

	sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='$_extramodules'/" "$startdir/nvidia.install"
}
