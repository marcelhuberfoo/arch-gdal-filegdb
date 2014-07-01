# Maintainer: SaultDon <sault.don gmail>
# Contributor: Dražen Odobašić <dodobasic at gmail>
pkgname=gdal-filegdb
_pkgname=gdal
pkgver=1.11.0
pkgrel=4
pkgdesc="A translator library for vector and raster geospatial data formats (PDF, FileGDB, KMZ support)"
arch=('i686' 'x86_64')
url="http://www.gdal.org/"
license=('custom')
depends=('geos' 'proj'
         'hdf5' 'netcdf'
         'libgeotiff' 'giflib' 'libjpeg' 'libpng' 'libtiff'
         'python2' 'python2-numpy'
         'cfitsio' 'curl'
         'sqlite3' 'libmysqlclient' 'unixodbc' 'libspatialite'
         'poppler'
         'opencl-headers'
         'filegdb-api'
         'json-c'
         'libkml-git')
makedepends=('perl'
             'swig'
             'chrpath'
             'svn'
             'doxygen')
optdepends=('postgresql-libs: postgresql database support')
options=('!libtool' '!makeflags')
changelog=$pkgname.changelog
source=("$_pkgname-$pkgver::svn+https://svn.osgeo.org/gdal/branches/1.11/gdal"
        'gdal-1.5.1-python-install.patch')
md5sums=('SKIP'
         '81afc1c26d29cee84aadb6924fe33861')
provides=("$_pkgname=$pkgver")
conflicts=("$_pkgname")

prepare() {
  cd "$srcdir/$_pkgname-$pkgver"

  patch -Np0 -i "$srcdir/gdal-1.5.1-python-install.patch"

# python2 fixes
  sed -i 's_python python1.5_python2 python python1.5_' configure
  for file in swig/python/{,osgeo/,samples/,scripts/}*.py; do
    sed -i 's_#!/usr/bin/env python_#!/usr/bin/env python2_' $file
  done

# Fix mandir
  sed -i "s|^mandir=.*|mandir='\${prefix}/share/man'|" configure
}
build() {
  cd "$srcdir/$_pkgname-$pkgver"
  export CPPFLAGS=-Dlinux
  export CFLAGS="$CFLAGS -fno-strict-aliasing"

# bug 23654
  export LDFLAGS="$LDFLAGS -Wl,--as-needed"
  
  ./configure --prefix=/usr --with-netcdf --with-libtiff --with-sqlite3 \
              --with-geotiff --with-mysql --with-python=/usr/bin/python2 \
              --without-libtool --with-curl --with-hdf5 --with-perl --with-geos \
              --with-png --with-poppler --with-spatialite --without-grass \
              --with-fgdb=/usr/lib/filegdb-api --with-libkml \
              --with-opencl --with-libjson-c \

# workaround for bug #13646
  sed -i 's/PY_HAVE_SETUPTOOLS=1/PY_HAVE_SETUPTOOLS=/g' ./GDALmake.opt
  sed -i 's/EXE_DEP_LIBS/KILL_EXE_DEP_LIBS/' apps/GNUmakefile

  make
  make man
}

package () {
  cd "$srcdir/$_pkgname-$pkgver"

  make DESTDIR="${pkgdir}" install
  make DESTDIR="${pkgdir}" install-man

  # install license
  install -D -m644 LICENSE.TXT "$pkgdir/usr/share/licenses/$_pkgname/LICENSE"

#FS15477 clean up junks
  rm -f "${pkgdir}"/usr/bin/*.dox
  rm -f "${pkgdir}"/usr/share/man/man1/_build_gdal_src_gdal-${pkgver}_apps_.1

# Remove RPATH
  chrpath --delete "${pkgdir}"/usr/lib/perl5/${CARCH}-linux-thread-multi/auto/Geo/OSR/OSR.so
  chrpath --delete "${pkgdir}"/usr/lib/perl5/${CARCH}-linux-thread-multi/auto/Geo/OGR/OGR.so
  chrpath --delete "${pkgdir}"/usr/lib/perl5/${CARCH}-linux-thread-multi/auto/Geo/GDAL/GDAL.so
  chrpath --delete "${pkgdir}"/usr/lib/perl5/${CARCH}-linux-thread-multi/auto/Geo/GDAL/Const/Const.so
}
