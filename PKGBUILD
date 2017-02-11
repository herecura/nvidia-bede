# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=375.26
_extramodules=4.10-BEDE-external
_current_linux_version=4.10rc7
_next_linux_version=4.11
pkgrel=13
pkgdesc="NVIDIA drivers for linux-bede"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
makedepends=(
    "linux-bede>=$_current_linux_version"
    "linux-bede-headers>=$_current_linux_version"
    "linux-bede<$_next_linux_version"
    "linux-bede-headers<$_next_linux_version"
    "nvidia-utils=$pkgver"
    "nvidia-libgl=$pkgver"
)
provides=('nvidia')
license=('custom')
options=(!strip)

source=('linux-4.10-rc1.patch' 'linux-4.10-rc1-x86_64.patch')
source_i686=("http://download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run")
source_x86_64=("http://download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")
#source_i686=("NVIDIA-Linux-x86-$pkgver.run::https://developer.nvidia.com/linux32bit")
#source_x86_64=("NVIDIA-Linux-x86_64-$pkgver-no-compat32.run::https://developer.nvidia.com/linux64bit")
sha512sums=('db48665e2e68bdf3e1381ff5b9b8c70056c6a2b9f6ed27d9882be2b62258d68c595f6230552f03b5addcde16cc3216722f3811a6fa4bfd9cb3f1ca9d0aa57ab5'
            'd3adcd8af6f1bd4672c40ae37a979798d6a00e9fcf4aef48a15b525201a22fa43bed91cc0aca57fe75f1673afb1f0018d8f31494c64592897fb51e0d090cca17')
sha512sums_i686=('3bc859a95469a45f3c627018248d83e178d160385c3d17d9f890b0d142ecd1220fb21c442e4fe7755b831227a9c820736f447b162acd9699819c6e8145d6d841')
sha512sums_x86_64=('f52f6597daa1eaf4cbd934d785da6028ef23ecef98e14746143e3738504f8d65b73788abbcf9fd812317fc2c53cdf1c4d4839de57fafdea1930a08c6b21f1992')

[[ "$CARCH" = "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
[[ "$CARCH" = "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"
#_folder=${_pkg//-no-compat32/}
_folder=${_pkg}

prepare() {
    [ -d "$_folder" ] && rm -rf "$_folder"
    sh $_pkg.run --extract-only
    cd $_folder
    # patch if needed
    patch -p1 -i "$srcdir/linux-4.10-rc1.patch"
    if [[ "$CARCH" = "x86_64" ]];
       then patch -p1 -i "$srcdir/linux-4.10-rc1-x86_64.patch"
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
        "nvidia-libgl=$pkgver"
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

