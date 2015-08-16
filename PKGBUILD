pkgname=mingw-w64-pango
pkgver=1.36.8
pkgrel=1
pkgdesc="A library for layout and rendering of text (mingw-w64)"
arch=(any)
url="http://www.pango.org"
license=("LGPL")
makedepends=(mingw-w64-gcc mingw-w64-pkg-config)
depends=(mingw-w64-harfbuzz mingw-w64-cairo)
options=(staticlibs !strip !buildflags)

source=("http://ftp.gnome.org/pub/gnome/sources/pango/${pkgver:0:4}/pango-${pkgver}.tar.xz")

sha256sums=('18dbb51b8ae12bae0ab7a958e7cf3317c9acfc8a1e1103ec2f147164a0fc2d07')

_architectures="i686-w64-mingw32 x86_64-w64-mingw32"



prepare() {
  cd "$srcdir/pango-$pkgver"
  sed -i 's/have_libthai=true/have_libthai=false/' configure
}



build() {
  for _arch in ${_architectures}; do
    export CXX=$_arch-g++
    unset LDFLAGS
    export CFLAGS="-O2 -pipe -mms-bitfields"
    export CXXFLAGS="${CFLAGS}"
    mkdir -p "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    "${srcdir}"/${pkgname#mingw-w64-}-${pkgver}/configure \
      --prefix=/usr/${_arch} \
      --build=$CHOST \
      --host=${_arch} \
      --with-included-modules=yes \
      --with-dynamic-modules=no \
      --disable-introspection \
      --disable-debug \
      --enable-shared \
      --enable-static
    make
  done
}



package() {
  for _arch in ${_architectures}; do
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    make DESTDIR="$pkgdir" install
    find "$pkgdir/usr/${_arch}" -name '*.exe' -o -name '*.bat' -o -name '*.def' -o -name '*.exp' | xargs -rtl1 rm
    find "$pkgdir/usr/${_arch}" -name '*.dll' | xargs -rtl1 ${_arch}-strip --strip-unneeded
    find "$pkgdir/usr/${_arch}" -name '*.a' -o -name '*.dll' | xargs -rtl1 ${_arch}-strip -g
    rm -r "$pkgdir/usr/${_arch}/share"
  done
}
