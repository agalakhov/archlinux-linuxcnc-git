# Maintainer:  Dimitris Kiziridis <ragouel at outlook dot com>

pkgname=linuxcnc-git
pkgver=2.8.0.r1390.gab3c8f7304
pkgrel=1
pkgdesc="Controls CNC machines. It can drive milling machines, lathes, 3d printers, laser cutters, plasma cutters, robot arms, hexapods, and more (formerly EMC2)"
arch=('i686' 'x86_64')
license=('GPL2')
url="http://linuxcnc.org"
depends=('bc'
         'bwidget'
         'libxaw'
         'python2-imaging'
         'python2-yapps2'
         'tkimg'
         'python2-gtkglext'
         'tclx'
         'xorg-server'
         'boost'
         'procps-ng'
         'psmisc')
makedepends=('intltool' 'git')
provides=('linuxcnc')
conflicts=('linuxcnc' 'linuxcnc-bin')
source=("${pkgname}::git+https://github.com/LinuxCNC/linuxcnc"
        'libtirpc.patch')
sha256sums=('SKIP'
            'bc95bafd67fad1c1d3722261bc586cdc612ec9e1597fadb95fa825c10550ac2c')

pkgver() {
  cd "${srcdir}/${pkgname}"
  git describe --long --tags | sed 's/^v//;s/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  cd "${srcdir}/${pkgname}/src"
  echo "export TCLLIBPATH=$TCLLIBPATH:/usr/lib/tcltk/linuxcnc" > ${pkgname}.sh
  find . -iname fixpaths.py -o -iname checkglade -o \
   -iname update_ini | xargs perl -p -i -e "s/python/python2/"
  patch -Np2 -i "${srcdir}/libtirpc.patch"
  ./autogen.sh
  ./configure --with-realtime=uspace \
   --without-libmodbus --prefix=/usr \
   --with-python=/usr/bin/python2.7 \
   --enable-non-distributable=yes
  # Linking time errors fix
  sed -i "163s|FileName|FileNameArr|" hal/classicladder/files_project.c
  sed -i "174s|FileName|FileNameArr|g" hal/classicladder/files_project.c
  sed -i "175s|FileName|FileNameArr|" hal/classicladder/files_project.c
}

build () {
  cd "${srcdir}/${pkgname}/src"
  make
}

package() {
  cd "${srcdir}/${pkgname}/src"
  DESTDIR=${pkgdir} make install 
  cp -PR "${srcdir}/${pkgname}/share/applications" "${pkgdir}/usr/share"
  mkdir -p "${pkgdir}/etc/xdg"
  cp -PR "${srcdir}/${pkgname}/share/menus" "${pkgdir}/etc/xdg/"
  install -Dm755 "${srcdir}/${pkgname}/src/${pkgname}.sh" \
   "${pkgdir}/etc/profile.d/${pkgname}.sh"
  sed -i "s|${srcdir}||" "${pkgdir}/usr/include/linuxcnc/config.h"
  sed -i "s|${srcdir}||" "${pkgdir}/usr/share/linuxcnc/Makefile.modinc"
}
