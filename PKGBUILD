# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede
pkgver=440.64
_current_linux_version=5.6.2
_next_linux_version=5.7
pkgrel=12
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
source=('kernel-5.6.patch')
sha512sums=('a622f4d784103d58f30c584976060ba499f794a0852c469da202314842495bdfbbcae8a510b534eec4477590a1181cae1b98d239a54a60ef2bd752b6ca8ebd1b')

# in case we need to do some patching
build() {
    _kernver="$(cat /usr/src/linux-bede/version)"

    # dkms need modification to be run as user
    cp -Lr /var/lib/dkms .
    echo "dkms_tree='$srcdir/dkms'" > dkms.conf

    (
        cd dkms/$_pkgname/${pkgver}/source
        patch -p2 -i "$srcdir/kernel-5.6.patch"
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
