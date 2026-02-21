# Maintainer: arbrandes <arbrandes@gmail.com>

pkgname=nanokontroller-git
_pkgname=nanokontroller
pkgver=0.1.0.r26.d80f95b
pkgrel=1
pkgdesc="Python app to turn the controllers on a Korg nanoKONTROL2 device into user actions on Linux"
arch=('any')
url="https://github.com/arbrandes/nanoKontroller"
license=('GPL3')
depends=('python' 'python-evdev' 'python-pulsectl' 'python-mido' 'python-rtmidi' 'alsa-lib')
makedepends=('git')
provides=("$_pkgname")
conflicts=("$_pkgname")
install="$_pkgname.install"
source=("$_pkgname::git+${url}.git"
        "nanokontroller.service"
        "nanokontroller-uinput.conf")
sha256sums=('SKIP'
            'SKIP'
            'SKIP')

pkgver() {
  cd "$_pkgname"
  printf "0.1.0.r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

package() {
  cd "$_pkgname"
  
  # Install script
  install -Dm755 nanoKontroller "$pkgdir/usr/bin/nanoKontroller"
  
  # Install udev rule
  install -Dm644 50-uinput.rules "$pkgdir/usr/lib/udev/rules.d/50-nanokontroller.rules"
  
  # Install modules-load.d config
  install -Dm644 "$srcdir/nanokontroller-uinput.conf" "$pkgdir/usr/lib/modules-load.d/nanokontroller.conf"
  
  # Install systemd user service
  install -Dm644 "$srcdir/nanokontroller.service" "$pkgdir/usr/lib/systemd/user/nanokontroller.service"
  
  # Install docs and license
  install -Dm644 README.md "$pkgdir/usr/share/doc/$_pkgname/README.md"
  install -Dm644 config-example.ini "$pkgdir/usr/share/doc/$_pkgname/config-example.ini"
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$_pkgname/LICENSE"
}
