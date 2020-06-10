# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=440.82
_current_linux_version=5.7.2
_next_linux_version=5.8
pkgrel=23
pkgdesc="NVIDIA drivers for linux-bede"
arch=('x86_64')
url="http://www.nvidia.com/"
makedepends=(
    "linux-bede>=$_current_linux_version"
    "linux-bede-headers>=$_current_linux_version"
    "linux-bede<$_next_linux_version"
    "linux-bede-headers<$_next_linux_version"
    "nvidia-dkms=$pkgver"
    "nvidia-utils=$pkgver"
)
provides=('nvidia')
license=('custom')
options=(!strip)
source=('kernel-5.7.patch')
sha512sums=('a010fc217ace313350aca05e3225c328ca98d5ce636d3742fc7a9e353e9af8d9cdd5424bee6dadbe57c8c0cb58f1f204129bf8ac25848256053cae31c286b8cd')

# in case we need to do some patching
build() {
    _kernver="$(cat /usr/src/linux-bede/version)"

    # dkms need modification to be run as user
    cp -Lr /var/lib/dkms .
    echo "dkms_tree='$srcdir/dkms'" > dkms.conf

    (
        cd dkms/$_pkgname/${pkgver}/source
        patch -p2 -i "$srcdir/kernel-5.7.patch"
    )
    # build host modules
    dkms --dkmsframework dkms.conf build "$_pkgname/${pkgver}" -k "$_kernver"
}

package() {
	depends=(
        "linux-bede>=$_current_linux_version"
        "linux-bede<$_next_linux_version"
        "nvidia-utils=$pkgver"
        "libglvnd"
    )

    local kernver=$(</usr/src/linux-bede/version)
    local extradir="/usr/lib/modules/$kernver/extramodules"
    install -dm755 "${pkgdir}${extradir}/$_pkgname"
    #cp -a "/var/lib/dkms/$_pkgname/kernel-$kernver-x86_64/module"/* \
        #"${pkgdir}${extradir}/$_pkgname/"
    # local home dkms
    cp -a "$srcdir/dkms/$_pkgname/$pkgver/$kernver/x86_64/module"/* \
        "${pkgdir}${extradir}/$_pkgname/"

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
}
