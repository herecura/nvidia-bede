# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=440.44
_current_linux_version=5.5
_next_linux_version=5.6
pkgrel=15
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
    "linux-5.5.patch"
)
sha512sums=('b183e8ac7d31317a5fe8e9395c58858f6c7828bad1879bdf67f9cd682099da0c2c1b5dbc0e35a3b39da80b383b81672b4fde0d53affc331f94bbccc0d6c2568c'
            '55452108bb63ef19b4c64c4e1f58da60a10ac9bd95d077a4472e3557be7e2d87182274e430260c88e36f915be467a97eb63d145fbdea293525e96db503953d33')

[[ "$CARCH" == "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"
#_folder=${_pkg//-no-compat32/}
_folder=${_pkg}

prepare() {
    [ -d "$_folder" ] && rm -rf "$_folder"
    sh $_pkg.run --extract-only
    cd $_folder
    # patch if needed
    patch -p1 -i "$srcdir/linux-5.5.patch"
}

build() {
    cd $_folder/kernel
    make SYSSRC=/usr/src/linux-bede
}

package() {
	depends=(
        "linux-bede>=$_current_linux_version"
        "linux-bede<$_next_linux_version"
        "nvidia-utils=$pkgver"
    )

    local extradir="/usr/lib/modules/$(</usr/src/linux-bede/version)/extramodules"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia.ko" \
        "${pkgdir}${extradir}/$_pkgname/nvidia.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-modeset.ko" \
        "${pkgdir}${extradir}/$_pkgname/nvidia-modeset.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-drm.ko" \
        "${pkgdir}${extradir}/$_pkgname/nvidia-drm.ko"

    if [[ "$CARCH" = "x86_64" ]]; then
        install -D -m644 "${srcdir}/${_folder}/kernel/nvidia-uvm.ko" \
            "${pkgdir}${extradir}/$_pkgname/nvidia-uvm.ko"
    fi

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    find "${pkgdir}" -name '*.ko' -exec xz {} +
}

