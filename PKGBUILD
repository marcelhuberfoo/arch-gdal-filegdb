# Maintainer: SaultDon <sault.don gmail>
# Contributor: Dražen Odobašić <dodobasic at gmail>

pkgname=gdal-filegdb
_pkgname=gdal
pkgver=1.10.1
pkgrel=4
pkgdesc="A translator library for vector and raster geospatial data formats (curl, PDF and FileGDB support)"
arch=('i686' 'x86_64')
url="http://www.gdal.org/"
license=('custom')
depends=('curl' 'geos' 'giflib' 'hdf5' 'libgeotiff' 'libjpeg' 'libpng' 'libtiff' 'netcdf'  'python2' 'python2-numpy' 'cfitsio' 'sqlite3' 'libmysqlclient' 'postgresql-libs' 'podofo' 'filegdb-api')
makedepends=('perl' 'swig' 'chrpath')
optdepends=('postgresql: postgresql database support'
            'mariadb: mariadb/mysql database support'
            'swig:  perl binding support'
	    'poppler: pdf conversion support'
	    'libspatialite: spatiallite support')
options=('!libtool' '!makeflags')
changelog=$pkgname.changelog
source=("http://download.osgeo.org/${_pkgname}/${pkgver}/${_pkgname}-${pkgver}.tar.gz"
	'gdal-1.5.1-python-install.patch'
        'http://trac.osgeo.org/gdal/raw-attachment/ticket/5191/5191.diff'
        '5270.diff::http://trac.osgeo.org/gdal/changeset/26533?format=diff&new=26533')
md5sums=('86b2c71a910d826a7fe6ebb43a532fb7'
         '81afc1c26d29cee84aadb6924fe33861'
         'b383a6d8cd24da2cce99d3f96d29c763'
         '6e6a177b3d1b8f536cfa998580d0da5f')
provides=('gdal=1.10.1')
conflicts=('gdal')

prepare() {
        cd "$srcdir/$_pkgname-$pkgver"
        patch -Np0 -i "$srcdir/gdal-1.5.1-python-install.patch"
        patch -Np0 -i "$srcdir/5191.diff"                         # http://trac.osgeo.org/gdal/ticket/5191
        patch -Np4 -i "$srcdir/5270.diff"			  # http://trac.osgeo.org/gdal/ticket/5270

        # python2 fixes
        for file in swig/python/{,osgeo/,samples/,scripts/}*.py; do
            sed -i 's_#!/usr/bin/env python_#!/usr/bin/env python2_' $file
        done
}
build() {
  cd ${srcdir}/$_pkgname-$pkgver

  ./configure --prefix=/usr --with-netcdf --with-libtiff --with-sqlite3 \
              --with-geotiff --with-mysql --with-python=/usr/bin/python2 \
              --without-libtool --with-curl --with-hdf5 --with-perl --with-geos \
              --with-png --with-podofo --with-spatialite \
              --with-fgdb=/usr/lib/filegdb-api --without-grass

  make
}

package () {
  cd ${srcdir}/$_pkgname-$pkgver

  make DESTDIR=${pkgdir} install

  # install license
  install -D -m644 LICENSE.TXT ${pkgdir}/usr/share/licenses/${_pkgname}/LICENSE

  #FS15477 clean up junks
  rm -f ${pkgdir}/usr/bin/*.dox

  # Remove RPATH
  chrpath --delete "${pkgdir}"/usr/lib/perl5/${CARCH}-linux-thread-multi/auto/Geo/OSR/OSR.so
  chrpath --delete "${pkgdir}"/usr/lib/perl5/${CARCH}-linux-thread-multi/auto/Geo/OGR/OGR.so
  chrpath --delete "${pkgdir}"/usr/lib/perl5/${CARCH}-linux-thread-multi/auto/Geo/GDAL/GDAL.so
  chrpath --delete "${pkgdir}"/usr/lib/perl5/${CARCH}-linux-thread-multi/auto/Geo/GDAL/Const/Const.so
}
