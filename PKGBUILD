# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=378.13
_extramodules=4.11-BEDE-external
_current_linux_version=4.11rc3
_next_linux_version=4.12
pkgrel=16.1
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
    'linux-4.11.patch'
    'linux-4.11-x86_64.patch'
)
source_i686=("http://download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run")
source_x86_64=("http://download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")
sha512sums=('b65fea54a9b46231a6cb138798cf72e95009d93486499580fac0660e976f60a2932dd60b1fac8662e64f7df913a4fd248f0739966fdf676624ba0c4d4ff16787'
            'd91bba6db2fa1c550ef6f8cc5989af712df5f4e2e9adcd2b7192e8433add1b0b81b6e169026fe6abb449dd5c42f3546960531603d656a6b1dd17eebd61c5ef8e')
sha512sums_i686=('b96d2558a1003a3c66cade3a1e16abd34d855c0e27cdebacdc0495e0ba3cd5c68bb84cc5f81bff1b9ddce36ac52e0dc125c56d868b77d7c8e2f606d559b13b4a')
sha512sums_x86_64=('b0ee6f1859d21e8f619e89fb75f2ace64bad5ba4852bc1b8a6148144fb2a917735a8272c0e528a8040b4d0db31a8203c6f698ea83c5cef41d8818d621d55eee3')

[[ "$CARCH" == "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
[[ "$CARCH" == "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"
#_folder=${_pkg//-no-compat32/}
_folder=${_pkg}

prepare() {
    [ -d "$_folder" ] && rm -rf "$_folder"
    sh $_pkg.run --extract-only
    cd $_folder
    # patch if needed
    patch -p1 --no-backup-if-mismatch -i "$srcdir/linux-4.11.patch"
    if [[ "$CARCH" == "x86_64" ]]; then
        patch -p1 --no-backup-if-mismatch -i "$srcdir/linux-4.11-x86_64.patch"
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

