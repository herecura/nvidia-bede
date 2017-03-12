# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=378.13
_extramodules=4.10-BEDE-external
_current_linux_version=4.10.2
_next_linux_version=4.11
pkgrel=10
pkgdesc="NVIDIA drivers for linux-bede"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
makedepends=(
    "linux-bede>=$_current_linux_version"
    "linux-bede-headers>=$_current_linux_version"
    "linux-bede<$_next_linux_version"
    "linux-bede-headers<$_next_linux_version"
    "nvidia-utils=$pkgver"
)
provides=('nvidia')
license=('custom')
options=(!strip)

source=(
    'NVIDIA-Linux-x86_64-378.13-kernel-4.10-rc8.patch'
    'NVIDIA-Linux-x86_64-378.13-kernel-4.10-rc8-x86_64.patch'
)
source_i686=("http://download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run")
source_x86_64=("http://download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")
sha512sums=('0b5c0ae678d5e453d473f0b206c204f8ee4c17b4a120b54be67703811ad865f55497e0e5b420ce175dc529afd66392d6dc8a8c84187aeae71490466bc1bd7686'
            '89ffaf92041e9548e20bbe7ea595365790931e80dec1c026a7a52df1592bb366a9a133b9d3e82baf322b6eb9321e92b2b06f7219afbfabc4f54851cbb7716008')
sha512sums_i686=('b96d2558a1003a3c66cade3a1e16abd34d855c0e27cdebacdc0495e0ba3cd5c68bb84cc5f81bff1b9ddce36ac52e0dc125c56d868b77d7c8e2f606d559b13b4a')
sha512sums_x86_64=('b0ee6f1859d21e8f619e89fb75f2ace64bad5ba4852bc1b8a6148144fb2a917735a8272c0e528a8040b4d0db31a8203c6f698ea83c5cef41d8818d621d55eee3')

[[ "$CARCH" = "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
[[ "$CARCH" = "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"
#_folder=${_pkg//-no-compat32/}
_folder=${_pkg}

prepare() {
    [ -d "$_folder" ] && rm -rf "$_folder"
    sh $_pkg.run --extract-only
    cd $_folder
    # patch if needed
    patch -p1 -i "$srcdir/NVIDIA-Linux-x86_64-378.13-kernel-4.10-rc8.patch"
    if [[ "$CARCH" = "x86_64" ]];
       then patch -p1 -i "$srcdir/NVIDIA-Linux-x86_64-378.13-kernel-4.10-rc8-x86_64.patch"
    fi
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

    # gzip all modules
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;
}

