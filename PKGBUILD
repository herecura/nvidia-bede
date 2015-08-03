# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=352.30
_extramodules=4.2-BEDE-external
pkgrel=1.2
pkgdesc="NVIDIA drivers for linux-bede"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
makedepends=('linux-bede>=4.2rc1' 'linux-bede<4.3' 'linux-bede-headers>=4.2rc1' 'linux-bede-headers<4.3' "nvidia-utils=$pkgver" "nvidia-libgl=$pkgver")
conflicts=('nvidia')
provides=('nvidia')
license=('custom')
install=nvidia.install
options=(!strip)

source=("nvidia-4.2.patch")
sha256sums=('01fe34a2eeb88057d51849098966e202f1ab94e548afe85ef25f533c8375e3c3')

source_i686=("http://download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run")
source_x86_64=("http://download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")

sha256sums_i686=('563eb858584f3eec38f137055039cd4addc7bb4c8f47f90e87a0cc1f88db32e3')
sha256sums_x86_64=('0f7acc89b2392c71cc8713b0291606052e0cd8971ee951901dde37387d85efc0')

[[ "$CARCH" = "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
[[ "$CARCH" = "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    [ -d "$_pkg" ] && rm -rf "$_pkg"
    sh $_pkg.run --extract-only
    cd $_pkg
    # patch if needed
    patch -Np1 -i ${srcdir}/nvidia-4.2.patch
}

build() {
    _kernver="$(cat /usr/lib/modules/$_extramodules/version)"
    cd $_pkg/kernel
    make SYSSRC=/usr/lib/modules/$_kernver/build module

    if [[ "$CARCH" = "x86_64" ]]; then
        cd uvm
        make SYSSRC=/usr/lib/modules/"${_kernver}/build" module
    fi
}

package() {
    depends=('linux-bede>=4.2rc1' 'linux-bede<4.3' "nvidia-utils=${pkgver}" "nvidia-libgl=$pkgver")

    install -Dm644 "$srcdir/$_pkg/kernel/nvidia.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia.ko"

    if [[ "$CARCH" = "x86_64" ]]; then
        install -D -m644 "${srcdir}/${_pkg}/kernel/uvm/nvidia-uvm.ko" \
            "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia-uvm.ko"
    fi

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    # gzip all modules
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;

    sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='$_extramodules'/" "$startdir/nvidia.install"
}

