# Contributor: skydrome <skydrome@protonmail.com>
# Maintainer:  skydrome <skydrome@protonmail.com>

pkgname=i2p-bin
pkgver=0.9.42
pkgrel=1
pkgdesc="A distributed anonymous network (pre-compiled binary)"
url="https://geti2p.net"
license=('GPL2')
arch=('any')
depends=('java-runtime>=8' 'java-service-wrapper')
#optdepends=('gtk2: for rrd graphs')
conflicts=('i2p' 'i2p-dev')
provides=('i2p')
backup=('opt/i2p/wrapper.config')
install='i2p.install'
noextract=("i2pinstall_${pkgver}.jar")
options=(!strip)

#_url="https://download.i2p2.de/releases/${pkgver}"
_url="https://launchpad.net/i2p/trunk/${pkgver}/+download"

source=("${_url}/i2pinstall_${pkgver}.jar"{,.sig}
        'i2prouter.service' 'i2prouter.sh' 'wrapper.config' 'router.config')

_hash=$(curl -Ls "https://geti2p.net/en/download" \
            |grep -A1 "<div class=\"hash\">" \
            |sed -e "s:.*<code>::" -e "s:</code>::" -e '5,5!d')

sha256sums=(${_hash:-'cb192e48c5f06839c99b71861364f3a9117b6b24f78f7f7c25d6716507c81bdf'}
            'SKIP'
            '9bb899ece87099716da29bac8b7da02916fc325699b68989e73c1fe333a6342f'
            'ea8f97e66461d591b1819eab39bbc40056b89ae12f7729b3dd9fd2ce088e5e53'
            'da8da9e30edb19fa276be37716b9e59061b1b5ef6b505c8c7c132f1c2b29d4b4'
            '1527afbadcf849ef551b3b7b68d1a29eec316ee620f5320f2933f73ee9924978')

# https://geti2p.net/en/get-involved/develop/release-signing-key
validpgpkeys=('2D3D2D03910C6504C1210C65EE60C0C8EE7256A8')

package() {
    source /etc/profile.d/jre.sh
    echo "INSTALL_PATH=${pkgdir}/opt/i2p" >install.properties
    java -jar i2pinstall_${pkgver}.jar \
         -options install.properties \
         -language eng

    cd "$pkgdir"

    install -dm755 "usr/bin"
    install -dm755 "opt/i2p/.tmp"

    install -Dm644 "$srcdir/router.config"     "opt/i2p/router.config"
    install -Dm644 "$srcdir/wrapper.config"    "opt/i2p/wrapper.config"
    install -Dm644 "$srcdir/i2prouter.sh"      "opt/i2p/i2prouter"
    install -Dm644 "$srcdir/i2prouter.service" "usr/lib/systemd/system/i2prouter.service"
    install -Dm644 "opt/i2p/man/eepget.1"      "usr/share/man/man1/eepget.1"
    install -Dm644 "opt/i2p/LICENSE.txt"       "usr/share/licenses/i2p/LICENSE"
    mv opt/i2p/licenses/*                      "usr/share/licenses/i2p/"

    ln -s /opt/i2p/{eepget,i2prouter} "usr/bin/"
    chmod +x opt/i2p/{eepget,i2prouter}

    chmod  -x opt/i2p/*.config
    chmod 755 opt/i2p
    chown  -R 985:985 opt/i2p

    echo 'u i2p 985 "I2P Router" /opt/i2p /bin/sh' |
    install -Dm644 /dev/stdin "usr/lib/sysusers.d/i2p.conf"
    echo 'd /run/i2p 0700 i2p i2p' |
    install -Dm644 /dev/stdin "usr/lib/tmpfiles.d/i2p.conf"

    sed -i opt/i2p/eepget \
        -e "s:$pkgdir/opt/i2p:/opt/i2p:g"
    sed -i opt/i2p/clients.config \
        -e "s:clientApp.3.startOnLoad=.*:clientApp.3.startOnLoad=false:" \
        -e "s:clientApp.4.startOnLoad=.*:clientApp.4.startOnLoad=false:"
    rm -rf opt/i2p/{Uninstaller,.installationinformation,INSTALL-headless.txt,LICENSE.txt,runplain.sh,licenses,man,i2psvc,lib/*wrapper*,scripts/home.i2p.i2prouter}
}
