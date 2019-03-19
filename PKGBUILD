# Maintainer: Moses Narrow <moe_narrow@use.startmail.com>
# Maintainer: Steve Skydev <steve@skycoin.net>
pkgname=cx
projectname=skycoin
pkgdesc="CX Skycoin Blockchain Programming Language"
pkgver=6.2
pkggopath="github.com/$projectname/$pkgname"
pkgrel=1
arch=('x86_64')
url="https://${pkggopath}"
license=()
makedepends=('unzip')
source=("https://github.com/skycoin/cx/releases/download/v0.6.2/cx-0.6.2-bin-linux-x64.zip")
sha256sums=('eca1452d7d24be43d122213c70e8c573a6debb32f9834dd6531068f48ab34422')
validpgpkeys=('DE08F924EEE93832DABC642CA8DC761B1C0C0CFC')

prepare() {
mkdir -p $srcdir/bin
mv cx-0.6.2-bin-linux-x64.zip $srcdir/bin/cx-0.6.2-bin-linux-x64.zip
cd $srcdir/bin
unzip cx-0.6.2-bin-linux-x64.zip
}

package() {
msg2 'installing CX'
options=(!strip staticlibs)
mkdir -p $pkgdir/usr/bin
mkdir -p $pkgdir/usr/lib/$projectname/go/bin
export GOPATH=$pkgdir/usr/lib/$projectname/go
export GOBIN=$pkgdir/usr/lib/$projectname/go/bin
install -Dm755 $srcdir/cx $pkgdir/usr/lib/$projectname/go/bin/cx
ln -rTsf $pkgdir/usr/lib/$projectname/go/bin/cx $pkgdir/usr/bin/cx
chmod 755 $pkgdir/usr/bin/cx
}
