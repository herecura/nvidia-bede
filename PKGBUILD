# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=367.35
_extramodules=4.7-BEDE-external
pkgrel=2.6
pkgdesc="NVIDIA drivers for linux-bede"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
makedepends=('linux-bede>=4.7rc1' 'linux-bede<4.8' 'linux-bede-headers>=4.7rc1' 'linux-bede-headers<4.8' "nvidia-utils=$pkgver" "nvidia-libgl=$pkgver")
provides=('nvidia')
license=('custom')
install=nvidia.install
options=(!strip)

source=(
    'linux-4.7.patch'
    'linux-4.7.x86_64.patch'
)
source_i686=("http://download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run")
source_x86_64=("http://download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")
#source_i686=("NVIDIA-Linux-x86-$pkgver.run::https://developer.nvidia.com/linux32bit")
#source_x86_64=("NVIDIA-Linux-x86_64-$pkgver-no-compat32.run::https://developer.nvidia.com/linux64bit")
sha256sums=('5a934a8ad03c7d0f61d3b1e863292bc9fefbbd3a4d60067b7d8adced42f7260d'
            'a79bd6f843e83ff56fad78d5ef34bcc5e28cde318f5b5cd40c074f408183b4dc')
sha256sums_i686=('38cb22fa85ef74ea960d3e5d644838cd961984ffc32bb0d052414cc7fa32e315')
sha256sums_x86_64=('8a6c554e9315e31e09d73fd60223ee4fbbe5507cd04c2e041c868a2080439354')

[[ "$CARCH" = "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
[[ "$CARCH" = "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"
#_folder=${_pkg//-no-compat32/}
_folder=${_pkg}

prepare() {
    [ -d "$_folder" ] && rm -rf "$_folder"
    sh $_pkg.run --extract-only
    cd $_folder
    # patch if needed
    patch -p1 -i "$srcdir/linux-4.7.patch"
    if [[ "$CARCH" = "x86_64" ]]; then
        patch -p1 -i "$srcdir/linux-4.7.x86_64.patch"
    fi
}

build() {
    _kernver="$(cat /usr/lib/modules/$_extramodules/version)"
    cd $_folder/kernel
    make SYSSRC=/usr/lib/modules/$_kernver/build module

}

package() {
    depends=('linux-bede>=4.7rc1' 'linux-bede<4.8' "nvidia-utils=${pkgver}" "nvidia-libgl=$pkgver")

    install -Dm644 "$srcdir/$_folder/kernel/nvidia.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia.ko"

    if [[ "$CARCH" = "x86_64" ]]; then
        install -D -m644 "${srcdir}/${_folder}/kernel/nvidia-uvm.ko" \
            "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia-uvm.ko"
    fi

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    # gzip all modules
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;

    sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='$_extramodules'/" "$startdir/nvidia.install"
}

