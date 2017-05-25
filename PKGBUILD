# Maintainer: Max Liebkies <mail at maxliebkies dot de>

pkgname=dotnet-lts
pkgver=1.0.4
pkgrel=1
pkgdesc="Provides the Long Term Service (LTS) versions of .NET core shared framework, i.e. coreclr and corefx."
arch=(x86_64)
url="https://www.microsoft.com/net/core"
license=('MIT')
groups=()
depends=('lldb' 'libunwind' 'icu' 'lttng-ust' 'openssl-1.0' 'curl')
makedepends=('cmake' 'make' 'clang<=4' 'llvm<=4' 'gettext')
provides=('dotnet=1.0.4')
conflicts=('dotnet-bin')
replaces=()
backup=()
options=(staticlibs)
install=

source=(
  "coreclr-${pkgver}.tar.gz::https://github.com/dotnet/coreclr/archive/v${pkgver}.tar.gz"
  "corefx-${pkgver}.tar.gz::https://github.com/dotnet/corefx/archive/v${pkgver}.tar.gz"
  "${pkgname}-${pkgver}.tar.gz::https://go.microsoft.com/fwlink/?LinkID=843462"
  'llvm-39-github-pull-8311.patch'
  'llvm-39-move.patch'
  'lttng-uts-40.patch'
  'no-libstdcpp-wrappers-for-stdlib.patch'
  'fix-dtor-call.patch'
  'fix-readdir-on-glibc-2.24.patch')
md5sums=('5839d1101d705c7d16606595cd8707be'
         'f4ca46c5b9e221fca4e79c5d897c112e'
         'da318b146ecf2db4bee6e81209dbcbf2'
         '2d8460befa48854b3adfa1a7328419c7'
         '070a3bb5091a78258c778f3f7c6f137a'
         'df24e4c22cbfcde5db82254f31f85cce'
         'cb11eadcd9895a27687dd5d2a4957d47'
         'b842b8210669c019a34bd85894a08027'
         '6fc63ac2e3c33f625120ecb5f28e5448')

  

prepare() {
  cd "${srcdir}/coreclr-${pkgver}"
  patch -p1 < "${srcdir}/llvm-39-github-pull-8311.patch"
  patch -p1 < "${srcdir}/llvm-39-move.patch"
  patch -p0 < "${srcdir}/lttng-uts-40.patch"
  patch -p1 < "${srcdir}/no-libstdcpp-wrappers-for-stdlib.patch"
  patch -p1 < "${srcdir}/fix-dtor-call.patch"
  
  cd "${srcdir}/corefx-${pkgver}"
  patch -p1 < "${srcdir}/fix-readdir-on-glibc-2.24.patch"
}

build() {
  cd "${srcdir}/coreclr-${pkgver}"
  ./build.sh x64 release

  cd "${srcdir}/corefx-${pkgver}"
  ./build.sh native x64 release
}

_coreclr_files=(
  'libclrjit.so'
  'libcoreclr.so'
  'libcoreclrtraceptprovider.so'
  'libdbgshim.so'
  'libmscordaccore.so'
  'libmscordbi.so'
  'libsosplugin.so'
  'libsos.so'
  'System.Globalization.Native.so'
)

_corefx_files=(
  'System.IO.Compression.Native.so'
  'System.Native.so'
  'System.Native.a'
  'System.Net.Http.Native.so'
  'System.Net.Security.Native.so'
  'System.Security.Cryptography.Native.so'
)

package() {
  mkdir -p "${pkgdir}/opt/dotnet/shared/Microsoft.NETCore.App/"
  
  # copy the original files from the binary archive
  cp --force --preserve=mode --recursive "${srcdir}/shared/Microsoft.NETCore.App/${pkgver}" "${pkgdir}/opt/dotnet/shared/Microsoft.NETCore.App/${pkgver}"

  for file in "${_coreclr_files[@]}"; do
    cp --force --preserve=mode "${srcdir}/coreclr-${pkgver}/bin/Product/Linux.x64.Release/${file}" "${pkgdir}/opt/dotnet/shared/Microsoft.NETCore.App/${pkgver}/"
  done

  for file in "${_corefx_files[@]}"; do
    cp --force --preserve=mode "${srcdir}/corefx-${pkgver}/bin/Linux.x64.Release/Native/${file}" "${pkgdir}/opt/dotnet/shared/Microsoft.NETCore.App/${pkgver}/"
  done

  chown -R root:root "${pkgdir}/opt/dotnet"
  find "${pkgdir}/opt/dotnet" -name *.dll -exec chmod 644 {} \;
  find "${pkgdir}/opt/dotnet" -name *.exe -exec chmod 755 {} \;
  find "${pkgdir}/opt/dotnet" -name *.so -exec chmod 755 {} \; 
}

# vim:set ts=2 sw=2 et:
