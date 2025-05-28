# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2024, 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer:
#   Truocolo
#     <truocolo@aol.com>
#     <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
# Maintainer:
#   Pellegrino Prevete (dvorak)
#     <pellegrinoprevete@gmail.com>
#     <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Maintainer :
#   Daniel Bermond
#     <dbermond@archlinux.org>
# Contributor:
#   Sven-Hendrik Haase
#     <sh@lutzhaase.com>
# Contributor:
#   Thomas Dziedzic
#     < gostrc at gmail >
# Contributor:
#   Laurent Carlier
#     <lordheavym@gmail.com>
# Contributor:
#   Allan McRae
#     <allan@archlinux.org>
# Contributor:
#   Zephyr

if [[ ! -v "_git" ]]; then
  _git="true"
fi
_pkg=mupen64plus
pkgname="${_pkg}"
pkgver=2.6.0
pkgrel=1
pkgdesc='Nintendo64 Emulator'
arch=(
  # arm
  'x86_64'
)
_http="https://github.com"
_ns="${_pkg}"
url="${_http}/${_ns}/${_pkg}-core"
license=(
  'GPL-2.0-or-later'
)
depends=(
  'freetype2'
  'glu'
  'hicolor-icon-theme'
  'libgl'
  'libpng'
  'libsamplerate'
  'minizip'
  'sdl2'
  'sdl2_net'
  'speexdsp'
  'vulkan-icd-loader'
  'zlib'
)
makedepends=(
  'nasm'
  'vulkan-headers'
)
_url="${url}"
_tag_name="pkgver"
_tag="${pkgver}"
_tarname="${_pkg}-core-${_tag}"
if [[ "${_git}" == "true" ]]; then
  makedepends+=(
    "git"
  )
  _src="${_tarname}::${url}#tag=${pkgver}"
fi
source=(
  "${_src}"
  "git+${_http}/${_pkg}/${_pkg}-rsp-hle.git#tag=${pkgver}"
  "git+${_http}/${_pkg}/${_pkg}-video-rice.git#tag=${pkgver}"
  "git+${_http}/${_pkg}/${_pkg}-video-glide64mk2.git#tag=${pkgver}"
  "git+${_http}/${_pkg}/${_pkg}-audio-sdl.git#tag=${pkgver}"
  "git+${_http}/${_pkg}/${_pkg}-input-sdl.git#tag=${pkgver}"
  "git+${_http}/${_pkg}/${_pkg}-ui-console.git#tag=${pkgver}"
  '010-mupen64plus-install-fix.patch'
)
sha256sums=(
  '1f8097d061e95142305674ff3fc88159e6b5d71554cb78bd8b58d8953dbea40d'
  'fca2b6250246f357d16869da7d1742b94895cf5983aa1d603fcf7ed1e651731c'
  '126078b0234de6203e5d176f5da8eb30fed71a14740682bac2721f2f7212c4a4'
  '60b95f4cb8d6e87d0a337199b7565fb69fce0a41b77b5b52eddc4c88f91858a1'
  '8ed4fe4904cc09509a5003569192fa528a4d0955d411c7c56aee062dac2cf1a8'
  '58c79bf34fb5d98decbdb267b6850fd7e50ea944019454bef81d885ca760e8e5'
  '3e68d1659e3cf8be343be803674a91895216f5079a0e912d0179192f07fef63f'
  '57d44edf46bafc6dbdf82e6db204c50bac1c2ea5c07aef7d3f84818f87e671a5'
)

prepare() {
  bsdtar \
    -xf \
    "${srcdir}/${_pkg}-core/tools/m64p_helper_scripts.tar.gz" \
    "m64p_install.sh"
  patch \
    -Np1 \
    -i \
    "010-mupen64plus-install-fix.patch"
}

build() {
  local \
    _component
  for _component in "core" \
	            "rsp-hle" \
		    "video-rice" \
		    "video-glide64mk2" \
		    "audio-sdl" \
		    "input-sdl" \
		    "ui-console"; do
    printf \
      '%s\n' \
      "  -> Building component '${_component}'..."
    make \
      -C \
        "${_pkg}-${_component}/projects/unix" \
      clean
    make \
      -C \
        "${_pkg}-${_component}/projects/unix" \
      PREFIX='/usr' \
      NETPLAY='1' \
      all
  done
}

package() {
  # set LDCONFIG since we are using fakeroot
  # and scripts run root commands by checking the uid
  ./m64p_install.sh \
    DESTDIR="${pkgdir}" \
    PREFIX='/usr' \
    LDCONFIG='true'
  ln \
    -s \
    "$(find \
         "${pkgdir}/usr/lib" \
	 -regex \
	   '.*libmupen64plus.so.[0-9]*' \
	 -exec \
	   basename {} +)" \
    "${pkgdir}/usr/lib/lib${_pkg}.so"
}
