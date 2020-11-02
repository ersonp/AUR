# Maintainer: Moses Narrow <moe_narrow@use.startmail.com>
# Maintainer: Rudi [KittyCash] <rudi@skycoinmail.com>
_projectname=skycoin
pkgname=skywire
_pkgname=${pkgname}
_githuborg=${_projectname}
pkgdesc=" Skywire Mainnet Node implementation. Develop Banch. Skycoin.com"
_pkggopath="github.com/${_githuborg}/${_pkgname}"
pkgver='autogenerated'
#pkgver='autogenerated'
pkgrel=4
#pkgrel=4
arch=('any')
url="https://${_pkggopath}"
license=()
makedepends=(git go npm python python2 skycoin-keyring)
#install=skywire.install
source=("git+${url}.git#branch=${BRANCH:-develop}"
#"skywire-scripts.tar.gz"
"PKGBUILD.sig")
sha256sums=('SKIP'
            'SKIP')
validpgpkeys=('DE08F924EEE93832DABC642CA8DC761B1C0C0CFC'  # Moses Narrow <moe_narrow@use.startmail.com>
'98F934F04F9334B81DFA3398913BBD5206B19620') #iketheadore skycoin <luxairlake@protonmail.com>
#tar -czvf skywire-scripts.tar.gz skywire-scripts
#updatepkgsums
export GOOS=linux
#export GOPATH="${srcdir}"
#export GOROOT="$builddir"
#export GOBIN="$GOROOT"/bin
#export GOROOT_FINAL=/usr/lib/go

export CGO_ENABLED=1

case "$CARCH" in
x86)      export GOARCH="386" GO386="387" ;;
x86_64)   export GOARCH="amd64" ;;
arm*)     export GOARCH="arm" ;;
armel)    export GOARCH="arm" GOARM="5" ;;
armhf)    export GOARCH="arm" GOARM="6" ;;
armv7)    export GOARCH="arm" GOARM="7" ;;
armv8)    export GOARCH="arm64" ;;
aarch64)  export GOARCH="arm64" ;;
mips)     export GOARCH="mips" ;;
mips64)   export GOARCH="mips64" ;;
mips64el) export GOARCH="mips64le" ;;
mipsel)   export GOARCH="mipsle" ;;
*)        return 1 ;;
	esac

pkgver() {
	cd "${srcdir}/${_pkgname}"
  local version=$(git describe --abbrev=0 | tr --delete v)
	local version=${version//-/_}
	local date=$(git log -1 --format="%cd" --date=short | sed s/-//g)
	local count=$(git rev-list --count HEAD)
	local commit=$(git rev-parse --short HEAD)
	echo "${version}_${date}.${count}_${commit}_${BRANCH:-develop}"
}

prepare() {
	#verify PKGBUILD signature
	gpg --verify ../PKGBUILD.sig ../PKGBUILD

  # https://wiki.archlinux.org/index.php/Go_package_guidelines
	mkdir -p ${srcdir}/go/src/github.com/${_githuborg}/ ${srcdir}/go/bin ${srcdir}/go/apps
  ln -rTsf ${srcdir}/${_pkgname} ${srcdir}/go/src/${_pkggopath}
  cd ${srcdir}/go/src/${_pkggopath}/
  #export GOPATH="${srcdir}"/go
  #export GOBIN=${GOPATH}/bin
  #export PATH=${GOPATH}/bin:${PATH}
}

build() {
  export GOPATH=${srcdir}/go
  export GOBIN=${GOPATH}/bin
  export GOAPPS=${GOPATH}/apps
  export PATH=${GOPATH}/bin
  cd ${srcdir}/go/src/${_pkggopath}
  ##build hypervisor UI
  #make install-deps-ui
  #make lint-ui
  #make build-ui
  ##build binaries
  _cmddir=${srcdir}/go/src/${_pkggopath}/cmd
  #using go build for determinism
  msg2 'building skychat binary'
	cd ${_cmddir}/apps/skychat
  go build -trimpath -ldflags '-extldflags ${LDFLAGS}' -ldflags=-buildid= -o $GOAPPS/ .
  msg2 'building skysocks binary'
  cd ${_cmddir}/apps/skysocks
  go build -trimpath -ldflags '-extldflags ${LDFLAGS}' -ldflags=-buildid= -o $GOAPPS/ .
  msg2 'building skysocks-client binary'
  cd ${_cmddir}/apps/skysocks-client
  go build -trimpath -ldflags '-extldflags ${LDFLAGS}' -ldflags=-buildid= -o $GOAPPS/ .
  msg2 'building vpn-client binary'
  cd ${_cmddir}/apps/vpn-client
  go build -trimpath -ldflags '-extldflags ${LDFLAGS}' -ldflags=-buildid= -o $GOAPPS/ .
  msg2 'building vpn-server binary'
  cd ${_cmddir}/apps/vpn-server
  go build -trimpath -ldflags '-extldflags ${LDFLAGS}' -ldflags=-buildid= -o $GOAPPS/ .
  msg2 'building skywire-visor binary'
  cd ${_cmddir}/skywire-visor
  go build -trimpath -ldflags '-extldflags ${LDFLAGS}' -ldflags=-buildid= -o $GOBIN/ .
  msg2 'building skywire-cli binary'
  cd ${_cmddir}/skywire-cli
  go build -trimpath -ldflags '-extldflags ${LDFLAGS}' -ldflags=-buildid= -o $GOBIN/ .
  msg2 'building setup-node binary'
	cd ${_cmddir}/setup-node
  go build -trimpath -ldflags '-extldflags ${LDFLAGS}' -ldflags=-buildid= -o $GOBIN/ .
  #msg2 'building hypervisor binary'  #hypervisor has been combined with the visor
	#cd ${_cmddir}/hypervisor
  #go build -trimpath -ldflags '-extldflags ${LDFLAGS}' -ldflags=-buildid= -o $GOBIN/ .
  #binary transparency
  cd $GOBIN
  msg2 'binary sha256sums'
  sha256sum $(ls)
  cd $GOAPPS
  sha256sum $(ls)

}

package() {
  #create directory trees
  mkdir -p ${pkgdir}/usr/bin/ ${pkgdir}/opt/skywire/bin/ ${pkgdir}/opt/skywire/apps/
  msg2 'installing binaries'
  _skywirebins=$( ls ${srcdir}/go/bin )
  for i in ${_skywirebins}; do
    install -Dm755 ${srcdir}/go/bin/${i} ${pkgdir}/opt/skywire/bin/${i}
      ln -rTsf ${pkgdir}/opt/skywire/bin/${i} ${pkgdir}/usr/bin/${i}
  done
  #check
  msg2 'testing `skywire-cli visor gen-config --help`'
  ${pkgdir}/opt/skywire/bin/skywire-cli visor gen-config --help
  _skywireapps=$( ls ${srcdir}/go/apps )
  for i in ${_skywireapps}; do
    install -Dm755 ${srcdir}/go/apps/${i} ${pkgdir}/opt/skywire/apps/${i}
    ln -rTsf ${pkgdir}/opt/skywire/apps/${i} ${pkgdir}/usr/bin/${i}
  done
  #install scripts - disabled pending review
  #install -Dm755 ${srcdir}/${_pkgname}-scripts/skywire-halt.sh ${pkgdir}/usr/bin/skywire-halt
  #install -Dm755 ${srcdir}/${_pkgname}-scripts/skywire.sh ${pkgdir}/usr/bin/skywire
  #install -Dm755 ${srcdir}/${_pkgname}-scripts/skywire-setuser.sh ${pkgdir}/usr/bin/skywire-setuser
  #install -Dm644  ${srcdir}/${_pkgname}-scripts/hypervisorconfig.PKGBUILD  ${pkgdir}/usr/lib/${_projectname}/${_pkgname}/hypervisorconfig/PKGBUILD
	#cd ${pkgdir}/usr/bin/
	#mv hypervisor ${_pkgname}-hypervisor
  #install the system.d services
  #install -Dm644 ${srcdir}/go/src/${_pkggopath}/init/${_pkgname}-hypervisor.service ${pkgdir}/usr/lib/systemd/system/${_pkgname}-hypervisor.service
  install -Dm644 ${srcdir}/go/src/${_pkggopath}/init/${_pkgname}-visor.service ${pkgdir}/usr/lib/systemd/system/${_pkgname}-visor.service
	#tls key and certificate generation
	install -Dm755 ${srcdir}/${_pkgname}-scripts/generate.sh ${pkgdir}/opt/skywire/ssl/generate.sh
  ln -rTsf ${pkgdir}/opt/skywire/ssl/generate.sh ${pkgdir}/usr/bin/skywire-tls-gen
	install -Dm644 ${srcdir}/${_pkgname}-scripts/certificate.cnf ${pkgdir}/opt/skywire/ssl/certificate.cnf

}
