# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=358.16
_extramodules=4.3-BEDE-external
pkgrel=4.1
pkgdesc="NVIDIA drivers for linux-bede"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
makedepends=('linux-bede>=4.3.3-2' 'linux-bede<4.4' 'linux-bede-headers>=4.3' 'linux-bede-headers<4.4' "nvidia-utils=$pkgver" "nvidia-libgl=$pkgver")
provides=('nvidia')
license=('custom')
install=nvidia.install
options=(!strip)

source=(
    'nvidia-drivers-pax-usercopy.patch'
    'nvidia-drivers-pax-constify.patch'
    'nvidia-358.09-randstruct.patch'
    'license-hack.patch'
)
source_i686=("http://download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run")
source_x86_64=("http://download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")

sha256sums=('e6df3944e379065a675d6ae4167cee34dc46b44ac752ab2c54ab3f2255604c4c'
            '46cd90fd3af38e7541ef76414ff2d5a93073099271a6eb63663e7ab07813c976'
            '4247b64b7e07131ed5eb5ec74c2305260367ed5b8ab2a250c4ed6ba9a54ae548'
            'beb9da74c9c4cb6cb2935d1c82094b17f7133a4553bca612d5dff3ed7921bf15')
sha256sums_i686=('d3a2842cbfb1163e20c658fbfaf5a235d5c9f035cd2d657f15df8a14b3fe80b1')
sha256sums_x86_64=('4f0f02d1eb123128d133a5fd00a5ff129b2ac0482f552e15eafa8baa943321f7')

[[ "$CARCH" == "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
[[ "$CARCH" == "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    [ -d "$_pkg" ] && rm -rf "$_pkg"
    sh $_pkg.run --extract-only
    cd $_pkg
    # patch if needed
    patch -Np1 -i "$srcdir/nvidia-drivers-pax-usercopy.patch"
    [[ "$CARCH" == "x86_64" ]] && patch -Np1 -i "$srcdir/nvidia-drivers-pax-constify.patch" || true
    patch -Np1 -i "$srcdir/nvidia-358.09-randstruct.patch"
    patch -Np1 -i "$srcdir/license-hack.patch"
}

build() {
    _kernver="$(cat /usr/lib/modules/$_extramodules/version)"
    cd $_pkg/kernel
    make SYSSRC=/usr/lib/modules/$_kernver/build module

}

package() {
    depends=('linux-bede>=4.3' 'linux-bede<4.4' "nvidia-utils=${pkgver}" "nvidia-libgl=$pkgver")

    install -Dm644 "$srcdir/$_pkg/kernel/nvidia.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia.ko"

    if [[ "$CARCH" = "x86_64" ]]; then
        install -D -m644 "${srcdir}/${_pkg}/kernel/nvidia-uvm.ko" \
            "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia-uvm.ko"
    fi

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    # gzip all modules
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;

    sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='$_extramodules'/" "$startdir/nvidia.install"
}

