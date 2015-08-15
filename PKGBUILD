# $Id$
# Maintainer: Nick Hu <nickhu00@gmail.com>
# Contributor: Sven-Hendrik Haase <sh@lutzhaase.com>
# Contributor: Daenyth <Daenyth+Arch [AT] gmail [DOT] com>
# Contributor: djnm <nmihalich [at} gmail dott com>
pkgname=dwarffortress-myne
pkgver=0.40.09
# example result: if $pkgver == 0.40.04 then $_pkgver == 40_04
_pkgver=${pkgver#0.}
_pkgver=${_pkgver//./_}
pkgrel=1
pkgdesc="A single-player fantasy game. You control a dwarven outpost or an adventurer in a randomly generated persistent world. Packed with the Myne tileset, some binary patches, and CLA's colour scheme"
arch=(i686 x86_64)
url="http://www.bay12games.com/dwarves/"
install="$pkgname.install"
license=('custom:dwarffortress')
depends=(gtk2 glu glew libsndfile openal sdl_ttf sdl_image)
makedepends=(unrar perl sed)
if [[ $CARCH == 'x86_64' ]]; then
  depends=(lib32-gtk2 lib32-glu lib32-glew lib32-libsndfile lib32-openal
  lib32-ncurses lib32-sdl_ttf lib32-sdl_image)
  optdepends=('lib32-nvidia-utils: If you have nvidia graphics'
  'lib32-catalyst-utils: If you have ATI graphics'
  'lib32-alsa-lib: for alsa sound'
  'lib32-libpulse: for pulse sound')
fi

source=(http://www.bay12games.com/dwarves/df_${_pkgver}_linux.tar.bz2
CLA_graphic_set_v17-STANDALONE.zip::"http://dffd.wimbli.com/download.php?id=5945&f=CLA_graphic_set_40.xx-v17-STANDALONE.zip"
'git+https://github.com/svenstaro/dwarf_fortress_unfuck.git'
dwarffortress-myne
dwarffortress-myne.desktop
dwarffortress-myne.png)
md5sums=('edd30099e468344aa49c18e771b1c5cf'
         'd463d4ed9e66e54d644e8b736c8985bd'
         'SKIP'
         '2265db5596c331c6f1e18f57bd4408c5'
         '6e0e792a0e5f3a3e4dc1f24f1fbd291c'
         'b1d51f82400073af9bb179e34a9209d0')

prepare() {
  # Convert from DOS to Unix
  for i in CLA/*/*/*.txt
  do
    perl -pi -e 's/\r\n/\n/g' $i
  done

  # Myne tileset and colour scheme
  sed -i 's/CLA/Myne/g' CLA/data/init/init.txt # Comment out this line for CLA graphics instead

  if [ -e df_linux-myne ]; then
    rm -rf df_linux-myne
  fi
  mv df_linux df_linux-myne
}

build() {
 cd $srcdir/dwarf_fortress_unfuck
 mkdir build && cd build
 cmake ..
 make
}

package() {
  install -dm755 $pkgdir/opt/
  cp -r df_linux-myne/ $pkgdir/opt/$pkgname
  rm -r $pkgdir/opt/$pkgname/df $pkgdir/opt/$pkgname/libs/* $pkgdir/opt/$pkgname/g_src

  cp -r CLA/* $pkgdir/opt/$pkgname/
  rm -r $pkgdir/opt/$pkgname/raw/graphics/example
  rm $pkgdir/opt/$pkgname/raw/graphics/graphics_example.txt

  # Yay for precompiled stuff with junk permissions! :D
  find $pkgdir/opt/$pkgname -type d -exec chmod 755 {} +
  find $pkgdir/opt/$pkgname -type f -exec chmod 644 {} +

  install -Dm755 $srcdir/df_linux-myne/libs/Dwarf_Fortress $pkgdir/opt/$pkgname/libs/Dwarf_Fortress
  install -Dm755 $srcdir/dwarf_fortress_unfuck/build/libgraphics.so $pkgdir/opt/$pkgname/libs/libgraphics.so
  install -Dm755 dwarffortress-myne $pkgdir/usr/bin/dwarffortress-myne

  # No idea why we need this. Really. This isn't being loaded dynamically, it's not linked and
  # in general there is no indication this is being used. However, it doesn't work without this symlink.
  [[ $CARCH == "x86_64" ]] && ln -s /usr/lib32/libpng.so $pkgdir/opt/$pkgname/libs/libpng.so.3
  [[ $CARCH == "i686" ]] && ln -s /usr/lib/libpng.so $pkgdir/opt/$pkgname/libs/libpng.so.3

  sed -i "s/^pkgname=.*/pkgname=$pkgname/" $pkgdir/usr/bin/$pkgname

  # Desktop launcher with icon
  install -Dm644 dwarffortress-myne.desktop $pkgdir/usr/share/applications/"$pkgname".desktop
  install -Dm644 dwarffortress-myne.png $pkgdir/usr/share/pixmaps/"$pkgname".png

  install -Dm644 df_linux-myne/readme.txt $pkgdir/usr/share/licenses/$pkgname/readme.txt
}

# vim:set ts=2 sw=2 et:
