# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=415.23
_extramodules=4.20-BEDE-external
_current_linux_version=4.20rc7
_next_linux_version=4.21
pkgrel=3
pkgdesc="NVIDIA drivers for linux-bede"
arch=('x86_64')
url="http://www.nvidia.com/"
makedepends=(
    "linux-bede>=$_current_linux_version"
    "linux-bede-headers>=$_current_linux_version"
    "linux-bede<$_next_linux_version"
    "linux-bede-headers<$_next_linux_version"
    "nvidia-utils=$pkgver"
    "libglvnd"
)
provides=('nvidia')
license=('custom')
options=(!strip)

source=(
    "http://us.download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run"
)
sha512sums=('ee8beacffa2d2e682d8bb31814712e90de97d4cf06416a2b750601490be7460fd693c54f7072477192910c63b4af4abbd26006303813d52f25fccb8ba1bf00eb')

[[ "$CARCH" == "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"
#_folder=${_pkg//-no-compat32/}
_folder=${_pkg}

prepare() {
    [ -d "$_folder" ] && rm -rf "$_folder"
    sh $_pkg.run --extract-only
    cd $_folder
    # patch if needed
}

build() {
    _kernver="$(cat /usr/lib/modules/$_extramodules/version)"
    cd $_folder/kernel
    make SYSSRC=/usr/lib/modules/$_kernver/build module
}

package() {
	depends=(
        "linux-bede>=$_current_linux_version"
        "linux-bede<$_next_linux_version"
        "nvidia-utils=$pkgver"
    )

    install -Dm644 "$srcdir/$_folder/kernel/nvidia.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-modeset.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia-modeset.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-drm.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia-drm.ko"

    if [[ "$CARCH" = "x86_64" ]]; then
        install -D -m644 "${srcdir}/${_folder}/kernel/nvidia-uvm.ko" \
            "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia-uvm.ko"
    fi

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    find "${pkgdir}" -name '*.ko' -exec xz {} +
}

