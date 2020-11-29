 # Maintainer: Moses Narrow <moe_narrow@use.startmail.com>
# Maintainer: Rudi [KittyCash] <rudi@skycoinmail.com>
_projectname=skycoin
pkgname=skywire
_pkgname=${pkgname}
_githuborg=${_projectname}
pkgdesc="Skywire Mainnet Node implementation. Develop Banch. Skycoin.com"
_pkggopath="github.com/${_githuborg}/${_pkgname}"
pkgver='autogenerated'
#pkgver='autogenerated'
pkgrel=16
#pkgrel=16
arch=('any')
url="https://${_pkggopath}"
license=()
makedepends=('git' 'go' 'musl' 'kernel-headers-musl') #disable signature check pending fixes#  'skycoin-keyring')
optdepends=('readonly-cache: distribute hypervisorkey package for cluster management')
install=skywire.install
_scripts=skywire-scripts
#scripts need at least this commit to work if current build to develop fails
#source=("git+${url}.git#branch=${BRANCH:-develop}"
source=("git+${url}.git#commit=d156980280fdb2ddfc8765ff77cdd55c0b7e9d9c"
"${_scripts}.tar.gz"  )
#'PKGBUILD.sig' #disable signature checking for now
#'PKGBUILD')
sha256sums=('SKIP'
            '794b5a7767c306fe1dba55a975c39d667cc0b5f4ba172698bda7f25a19a867bc')
#            'SKIP'
#            'SKIP')
#validpgpkeys=('DE08F924EEE93832DABC642CA8DC761B1C0C0CFC')  # Moses Narrow <moe_narrow@use.startmail.com>
#'98F934F04F9334B81DFA3398913BBD5206B19620') #iketheadore skycoin <luxairlake@protonmail.com>

#tar -czvf skywire-scripts.tar.gz skywire-scripts
#updatepkgsums

pkgver() {
	cd "${srcdir}/${_pkgname}"
  local _version=$(git describe --abbrev=0 | tr --delete v)
	local _version=${_version//-/_}
	local _date=$(git log -1 --format="%cd" --date=short | sed s/-//g)
	local _count=$(git rev-list --count HEAD)
	local _commit=$(git rev-parse --short HEAD)
	echo "${_version}_${_date}.${_count}_${_commit}_${BRANCH:-develop}"
}

prepare() {
	#verify PKGBUILD signature
#	gpg --verify ${srcdir}/PKGBUILD.sig ${srcdir}/PKGBUILD   #comment out to disable signature check

  # https://wiki.archlinux.org/index.php/Go_package_guidelines
	mkdir -p ${srcdir}/go/src/github.com/${_githuborg}/ ${srcdir}/go/bin ${srcdir}/go/apps
  ln -rTsf ${srcdir}/${_pkgname} ${srcdir}/go/src/${_pkggopath}
  cd ${srcdir}/go/src/${_pkggopath}/
}

build() {
  export GOPATH=${srcdir}/go
  export GOBIN=${GOPATH}/bin
  export GOAPPS=${GOPATH}/apps
  cd ${srcdir}/go/src/${_pkggopath}
  export GOOS=linux
  export CGO_ENABLED=1
#use musl-gcc for static compilation
  export CC=musl-gcc

  #create the skywire binaries
  cd ${srcdir}/go/src/${_pkggopath}
  _cmddir=${srcdir}/go/src/${_pkggopath}/cmd
  #static compilation ; need to re-evaluate build determinism
  _msg2 'building skychat binary'
	cd ${_cmddir}/apps/skychat
  go build -trimpath --ldflags '-linkmode external -extldflags "-static" -buildid=' -o $GOAPPS/ .
  _msg2 'building skysocks binary'
  cd ${_cmddir}/apps/skysocks
  go build -trimpath --ldflags '-linkmode external -extldflags "-static" -buildid=' -o $GOAPPS/ .
  _msg2 'building skysocks-client binary'
  cd ${_cmddir}/apps/skysocks-client
  go build -trimpath --ldflags '-linkmode external -extldflags "-static" -buildid=' -o $GOAPPS/ .
  _msg2 'building vpn-client binary'
  cd ${_cmddir}/apps/vpn-client
  go build -trimpath --ldflags '-linkmode external -extldflags "-static" -buildid=' -o $GOAPPS/ .
  _msg2 'building vpn-server binary'
  cd ${_cmddir}/apps/vpn-server
  go build -trimpath --ldflags '-linkmode external -extldflags "-static" -buildid=' -o $GOAPPS/ .
  _msg2 'building skywire-visor binary'
  cd ${_cmddir}/skywire-visor
  go build -trimpath --ldflags '-linkmode external -extldflags "-static" -buildid=' -o $GOBIN/ .
  _msg2 'building skywire-cli binary'
  cd ${_cmddir}/skywire-cli
  go build -trimpath --ldflags '-linkmode external -extldflags "-static" -buildid=' -o $GOBIN/ .
  _msg2 'building setup-node binary'
	cd ${_cmddir}/setup-node
  go build -trimpath --ldflags '-linkmode external -extldflags "-static" -buildid=' -o $GOBIN/ .
  #_msg2 'building hypervisor binary'  #hypervisor has been combined with the visor
	#cd ${_cmddir}/hypervisor
  #go build -trimpath -ldflags '-extldflags ${LDFLAGS}' -ldflags=-buildid= -o $GOBIN/ .
  #binary transparency
  cd $GOBIN
  _msg2 'binary sha256sums'
  sha256sum $(ls)
  cd $GOAPPS
  sha256sum $(ls)

}

package() {
  #create directory trees
  _skydir="opt/skywire"
  _skyapps="${_skydir}/apps"
  _systemddir="etc/systemd/system"
  _skybin="${_skydir}/bin"
  mkdir -p ${pkgdir}/usr/bin/
  mkdir -p ${pkgdir}/${_skydir}/bin/
  mkdir -p ${pkgdir}/${_skydir}/apps/
  mkdir -p ${pkgdir}/${_skydir}/ssl
  mkdir -p ${pkgdir}/${_skydir}/local
  mkdir -p ${pkgdir}/${_skydir}/dmsgpty
  mkdir -p ${pkgdir}/${_skydir}/${_pkgname}
  mkdir -p ${pkgdir}/${_skydir}/transport_logs

  _msg2 'installing binaries'
  _skywirebins=$( ls ${srcdir}/go/bin )
  for i in ${_skywirebins}; do
    install -Dm755 ${srcdir}/go/bin/${i} ${pkgdir}/${_skybin}/${i}
      ln -rTsf ${pkgdir}/${_skybin}/${i} ${pkgdir}/usr/bin/${i}
  done

  _skywireapps=$( ls ${srcdir}/go/apps )
  for i in ${_skywireapps}; do
    install -Dm755 ${srcdir}/go/apps/${i} ${pkgdir}/${_skyapps}/${i}
    ln -rTsf ${pkgdir}/${_skyapps}/${i} ${pkgdir}/usr/bin/${i}
  done
  #install scripts
  install -Dm755 ${srcdir}/${_scripts}/skywire-halt.sh ${pkgdir}/usr/bin/skywire-halt
  install -Dm755 ${srcdir}/${_scripts}/skywire-setuser.sh ${pkgdir}/usr/bin/skywire-setuser

  #install the satellite PKGBUILD for distributing the hypervisor key and it's script
  install -Dm755 ${srcdir}/${_scripts}/keypkg-gen.sh ${pkgdir}/usr/bin/keypkg-gen
  install -Dm755  ${srcdir}/${_scripts}/hypervisorkey.PKGBUILD  ${pkgdir}/${_skydir}/hypervisorkey/PKGBUILD
  install -Dm755 ${srcdir}/${_scripts}/hypervisorkey.install ${pkgdir}/${_skydir}/hypervisorkey/hypervisorkey.install

  #rename visor to skywire
	mv ${pkgdir}/usr/bin/${_pkgname}-visor ${pkgdir}/usr/bin/${_pkgname}

  #install the system.d services
  #install -Dm644 ${srcdir}/go/src/${_pkggopath}/init/${_pkgname}-hypervisor.service ${pkgdir}/usr/lib/systemd/system/${_pkgname}-hypervisor.service
  #install -Dm644 ${srcdir}/go/src/${_pkggopath}/init/${_pkgname}-visor.service ${pkgdir}/usr/lib/systemd/system/${_pkgname}-visor.service

  #install the patched system.d services
  install -Dm644 ${srcdir}/${_scripts}/${_pkgname}.service ${pkgdir}/usr/lib/systemd/system/${_pkgname}.service
  install -Dm644 ${srcdir}/${_scripts}/${_pkgname}-visor.service ${pkgdir}/usr/lib/systemd/system/${_pkgname}-visor.service

  #tls key and certificate generation
  #install -Dm755 ${srcdir}/${_pkgname}/static/skywire-manager-src/ssl/generate-1.sh ${pkgdir}/${_skydir}/ssl/generate.sh
  install -Dm755 ${srcdir}/${_scripts}/generate.sh ${pkgdir}/${_skydir}/ssl/generate.sh
  ln -rTsf ${pkgdir}/${_skydir}/ssl/generate.sh ${pkgdir}/usr/bin/skywire-tls-gen
	install -Dm644 ${srcdir}/${_pkgname}/static/skywire-manager-src/ssl/certificate.cnf ${pkgdir}/${_skydir}/ssl/certificate.cnf
}


_msg2() {
	(( QUIET )) && return
	local mesg=$1; shift
	printf "${BLUE}  ->${ALL_OFF}${BOLD} ${mesg}${ALL_OFF}\n" "$@"
}


#chck() {
#check
#_msg2 'testing `skywire-cli visor gen-config --help`'
#${pkgdir}/opt/skywire/bin/skywire-cli visor gen-config --help
#}
