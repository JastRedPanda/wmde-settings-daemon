# Maintainer: WMDE <https://wmde.fun>
# Contributor: System76 <info@system76.com> (original cosmic-settings-daemon)
#
# Builds our fork Lin-WMDE/wmde-settings-daemon (branch wmde). WMDE settings
# backend daemon: brightness/audio/theme/xkb/geoclue integration + varlink audio
# server. Installs alongside cosmic-settings-daemon: own binary (wmde-settings-daemon),
# own D-Bus/config id (fun.wmde.SettingsDaemon) and own varlink socket
# (fun.wmde.Settings), so NO conflicts/replaces/provides of cosmic-settings-daemon
# and NO compat symlinks. Workspace member library crates keep their upstream
# names/dirs (audio-*, config, cosmic-settings-daemon-config, geonames,
# cosmic-pipewire, varlink-server) - they are consumed only in-tree here.
pkgname=wmde-settings-daemon
pkgver=0.1.0
pkgrel=1
pkgdesc="WMDE settings daemon (fork of cosmic-settings-daemon) - owns fun.wmde.SettingsDaemon"
arch=('x86_64')
url="https://wmde.fun"
license=('GPL-3.0-or-later')
# runtime: pipewire (audio backend), libpulse (pactl helper), udev/systemd-libs
# (backlight + logind), libxkbcommon (xkb layout switching), dbus, polkit rules,
# ddcutil (external monitor brightness via ddc-hi). Verify with namcap after build.
depends=('glibc' 'gcc-libs' 'pipewire' 'libpulse' 'libxkbcommon'
         'systemd-libs' 'dbus' 'ddcutil')
makedepends=('rust' 'cargo' 'git' 'make' 'clang' 'lld' 'pkgconf'
             'pipewire' 'libxkbcommon' 'systemd')
optdepends=('polkit: privileged locale/keyboard changes'
            'geoclue: automatic timezone and night-light location'
            'wmde-osd: headphone-confirmation and OSD prompts')
source=("$pkgname::git+https://github.com/Lin-WMDE/wmde-settings-daemon.git#branch=wmde")
sha256sums=('SKIP')

pkgver() {
  cd "$srcdir/$pkgname"
  git describe --long --tags --abbrev=7 2>/dev/null | sed 's/^epoch-//;s/^v//;s/\([^-]*-g\)/r\1/;s/-/./g' ||
    printf '0.1.0.r%s.g%s' "$(git rev-list --count HEAD)" "$(git rev-parse --short=7 HEAD)"
}

build() {
  cd "$srcdir/$pkgname"
  # x86-64-v3 (AVX2/BMI2) baseline for the WMDE repo; runs on Haswell+ (and the VM).
  export RUSTFLAGS="${RUSTFLAGS:+$RUSTFLAGS }-C target-cpu=x86-64-v3"
  make
}

package() {
  cd "$srcdir/$pkgname"
  # installs /usr/bin/wmde-settings-daemon, the system_actions defaults under
  # /usr/share/wmde/fun.wmde.Settings.Shortcuts/v1/ and the polkit rule
  # /usr/share/polkit-1/rules.d/wmde-settings-daemon.rules .
  make DESTDIR="$pkgdir" prefix=/usr install
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
