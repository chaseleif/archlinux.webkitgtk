# Maintainer: Fredy García <frealgagu at gmail.com>
# Contributor: Chih-Hsuan Yen <yan12125@archlinux.org>
# Contributor: foutrelis
# Contributor: Andreas Radke <andyrtr@archlinux.org>
# Contributor: Chiwan Park <chiwanpark@hotmail.com>

pkgname=webkitgtk
pkgver=2.4.11
pkgrel=27
epoch=3
pkgdesc="Legacy Web content engine for GTK+ 3"
arch=("armv7h" "i686" "x86_64")
url="https://${pkgname}.org/"
license=("custom")
depends=("enchant>=2.2" "geoclue2" "gst-plugins-base-libs" "gtk3" "harfbuzz-icu" "libgl" "libsecret" "libsoup" "libwebp" "libxslt" "libxt")
optdepends=(
  "gst-libav: nonfree media decoding"
  "gst-plugins-base: free media decoding"
  "gst-plugins-good: media decoding"
  "gtk2: Netscape plugin support"
)
makedepends=("gobject-introspection" "gperf" "gtk2" "mesa" "ruby")
provides=("${pkgname}3=${pkgver}" "libwebkit3=${pkgver}")
conflicts=("${pkgname}3" "libwebkit3")
replaces=("${pkgname}3" "libwebkit3")
options=("!emptydirs" "!lto")
install="${pkgname}.install"
source=(
  "https://${pkgname}.org/releases/${pkgname}-${pkgver}.tar.xz"
  "${pkgname}-2.4.9-abs.patch"
  "enchant-2.x.patch"
  "icu59.patch"
  "pkgconfig-enchant-2.patch"
  "icu68.patch"
  "gtk-doc.patch"
  "grammar.patch"
  "glib-2.68.0.patch"
  "volatile.patch"
  "rubyasm.patch"
  "wtfstd17.patch"
  "compilerflags.patch"
  "webkitextern.patch"
  "xmlconst.patch"
  "bison3.7.patch"
)
sha256sums=(
  "588aea051bfbacced27fdfe0335a957dca839ebe36aa548df39c7bbafdb65bf7"
  "e40f1e08665e1646ebc490141678f26c9c4a2792207fdf7c05978547eae9f61c"
  "7e37e059f071aaef93e387675de1a0c6a3dcf61ef67a3221a078caca69e22079"
  "4e94e35b036f8a87a64e02d747d4103c0553dfe637fda372785c2b95211445ca"
  "a1e2f24b28273746b2fbaecef42495f6314c76b16a446c22dc123e6a3afb58c8"
  "d1a9ccc1ae5cb042bc47ae846ff84513ca7b9c7bc999c546ffba48572f0373a0"
  "7341eb4c229656046be6ac526f94b9f4a742a66178412caf22a988677f5bf9d9"
  "5a62fbd0df69c6951562e72cd7b3c58cae7f2807338ced7b4a1973440b3dd193"
  "db202fedd72a21318646c561afcd76656ea8ba6b1d641363fc7b69ae5687aa28"
  "013bf12e0ab79664c74f1ef3c299f564cd1f47feab2a3daa5ab80db1c5aebfa1"
  "c56be904dca926b656236bbed3fb918d6ecf9ec41553aaea6daccbe9bd20068c"
  "16b5eace4935ec0d9c704e6972bd793bc3fc5f442a9896de7300eee7e837383a"
  "e2005ded1123615d56b0147110a9d4eff0a6a10cf5b756271d7a584e3e647924"
  "be8d20d4eb73b9a5540a710a90a0f55d31e0ed2ec188a62590c808ce5c055493"
  "0a36cc9a84715105579a028fae87ee5d770456bb50f7321f6fcde5d24c6c214f"
  "9f796028ba0873fd55ea1693dec5896bc0f955d0cda062e2b326d62dbda45140"
)

prepare() {
  mkdir -p "${srcdir}/build-gtk" "${srcdir}/path"
  ln -rTsf "/usr/bin/python2" "${srcdir}/path/python"

  cd "${srcdir}/${pkgname}-${pkgver}"
  patch -Np1 -i "${srcdir}/${pkgname}-2.4.9-abs.patch"
  patch -Np1 -i "${srcdir}/enchant-2.x.patch"
  patch -Np1 -i "${srcdir}/icu59.patch"
  # https://www.archlinux.org/todo/enchant-221-rebuild/
  patch -Np1 -i "${srcdir}/pkgconfig-enchant-2.patch"
  patch -Np1 -i "${srcdir}/icu68.patch"
  patch -Np1 -i "${srcdir}/gtk-doc.patch"

  # Needed as autotools-related files are patched
  autoreconf -ifv

  # https://www.linuxquestions.org/questions/slackware-14/sbo-scripts-not-building-on-current-read-1st-post-pls-4175561999/page195.html#post6160562
  patch -Np1 -i "${srcdir}/grammar.patch"

  # glib>=2.68.0
  patch -Np1 -i "${srcdir}/glib-2.68.0.patch"

  # gcc11+ compiler volatile patch
  patch -Np1 -i "${srcdir}/volatile.patch"

  patch -Np1 -i "${srcdir}/rubyasm.patch"
  patch -Np1 -i "${srcdir}/wtfstd17.patch"
  patch -Np1 -i "${srcdir}/compilerflags.patch"
  patch -Np1 -i "${srcdir}/webkitextern.patch"
  patch -Np1 -i "${srcdir}/xmlconst.patch"
  patch -Np1 -i "${srcdir}/bison3.7.patch"
}

build() (
  cd "${srcdir}/build-gtk"

  PATH="${srcdir}/path:${PATH}"

  CXXFLAGS+=" -fno-delete-null-pointer-checks"
  CFLAGS+=" -fno-delete-null-pointer-checks"

  CFLAGS+=" -Wno-expansion-to-defined -Wno-class-memaccess"
  CXXFLAGS+=" -Wno-expansion-to-defined -Wno-class-memaccess -DUPRV_BLOCK_MACRO_BEGIN=\"\" -DUPRV_BLOCK_MACRO_END=\"\""

  "${srcdir}/${pkgname}-${pkgver}/configure" \
    --prefix=/usr \
    --libexecdir=/usr/lib/${pkgname} \
    --enable-introspection \
    --disable-webkit2 \
    --disable-gtk-doc

  # https://bugzilla.gnome.org/show_bug.cgi?id=655517
  sed -i "s/ -shared / -Wl,-O1,--as-needed\0/g" "${srcdir}/build-gtk/libtool"

  make all stamp-po
)

package() {
  make -C "${srcdir}/build-gtk" DESTDIR="${pkgdir}" install
  install -Dm644 "${srcdir}/${pkgname}-${pkgver}/Source/WebKit/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
