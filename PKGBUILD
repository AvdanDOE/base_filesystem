# Maintainer: Sébastien Luttringer
# Contributor: Tom Gundersen <teg@jklm.no>

pkgname=filesystem
pkgver=$(date +%Y%m%d.%H%M).$(echo $RANDOM | md5sum | head -c 10; echo;).dogfood
pkgrel=1
pkgdesc='Base Arch Linux files'
arch=('x86_64')
license=('GPL')
url='https://archlinux.org'
depends=('iana-etc')
backup=('etc/crypttab' 'etc/fstab' 'etc/group' 'etc/gshadow' 'etc/host.conf'
        'etc/hosts' 'etc/issue' 'etc/ld.so.conf' 'etc/nsswitch.conf'
        'etc/passwd' 'etc/profile' 'etc/resolv.conf' 'etc/securetty'
        'etc/shadow' 'etc/shells')
source=('crypttab' 'env-generator' 'fstab' 'group' 'gshadow' 'host.conf' 'hosts'
        'issue' 'ld.so.conf' 'locale.sh' 'nsswitch.conf' 'os-release'
        'passwd' 'profile' 'resolv.conf' 'securetty' 'shadow' 'shells' 'sysctl'
        'sysusers' 'tmpfiles' 'avdanos-logo.svg' 'avdanos-logo.png'
        'avdanos-logo-text.svg' 'avdanos-logo-text-dark.svg')
sha256sums=('e03bede3d258d680548696623d5979c6edf03272e801a813c81ba5a5c64f4f82'
            'ed0cb4f1db4021f8c3b5ce78fdf91d2c0624708f58f36c9cf867f4d93c3bc6da'
            'e54626e74ed8fee4173b62a545ab1c3a3a069e4217a0ee8fc398d9933e9c1696'
            '244f0718ee2a9d6862ae59d6c18c1dd1568651eada91a704574fa527fbac2b3a'
            '90d879374f77bac47f132164c1e7fc4892e994ff1d1ac376efa0c1c26ea37273'
            '4d7b647169063dfedbff5e1e22cee77bd1a4183dbcfd5e802e68939da4bbf733'
            'd9cd8a77d9e0aa5e90d7f4ed74c8745c17b525e720e28e4c44364150003c35f9'
            'c774dbbcaea38ee1c1141d0daf82aa8177bfb26aca896d6f0c4ccdc902f6ac42'
            'dad04a370e488aa85fb0a813a5c83cf6fd981ce01883fc59685447b092de84b5'
            '8ca2d8eef6fb5143c9ef7e9174ccfef59ac7ad2deee243574cd10c763156cc10'
            'c8ee7a9faf798caab178ec51afae4146f1efd8a716b7acedf28345b6c75f9697'
            'a54d010860b3e9c4b8ccc282e61da5b7118c7547697151aaca36ee771d323dc9'
            '5e06477834f51abf42ea4e8dc199632afc6afbfd8c44354685a271e9a48d2c0a'
            '5e4088ad8d0853d390fa303f6be8c3f69c33834200cba9e90f7849f1993ca8d0'
            '5557d8e601b17a80d1ea7de78a9869be69637cb6a02fbfe334e22fdf64e61d4c'
            'd88be2b45b43605ff31dd83d6a138069b6c2e92bc8989b7b9ab9eba8da5f8c7b'
            '6e13705ac4d6f69cdba118c6b70c722346fd3c45224133e6bbfe28aca719563c'
            'c390b31fffc4a2b5d78ae8c89f5317aadef1f71baac09cfb467b675db1406d61'
            '89e43a0b7028f52d5c8e7fb961d962c4b4f4e9595880a6157274ddb2c7c0b6b4'
            'b5b28f395583d141d88c0b955cd05124f9b8cdf003feab01e55885b8e8c1303e'
            'f17efd1c6480c5db1fdab4a0e3cfcd4baa44a3e1ae1210aebeb40f8d5c82f742'
            '3ffe8ea4e98db43a3ec4dcca55fd4009cd8b8d220f0996aef7a5b427fdf65234'
            '3f48779141b68a81e07fee710a42025d4f67b16240295aa4cf148a7ba99cab3c'
            '601069e6e8920309178c397fd8cebe43410827d01899d31777d13212f0dfacf8'
            '96e3cc81623c0537a19799f9eefa966fe46ff5f28a9dc7af1187990973baa127')

package() {
  cd "$pkgdir"

  # setup root filesystem
  for d in boot dev etc home mnt usr var opt srv/http run; do
    install -d -m755 $d
  done
  install -d -m555 proc
  install -d -m555 sys
  install -d -m0750 root
  install -d -m1777 tmp
  # vsftpd won't run with write perms on /srv/ftp
  # ftp (uid 14/gid 11)
  install -d -m555 -g 11 srv/ftp

  # setup /etc and /usr/share/factory/etc
  install -d etc/{ld.so.conf.d,skel,profile.d} usr/share/factory/etc
  for f in fstab group host.conf hosts issue ld.so.conf nsswitch.conf \
  passwd resolv.conf securetty shells profile; do
    install -m644 "$srcdir"/$f etc/
    install -m644 "$srcdir"/$f usr/share/factory/etc/
  done
  ln -s ../proc/self/mounts etc/mtab
  for f in gshadow shadow crypttab; do
    install -m600 "$srcdir"/$f etc/
    install -m600 "$srcdir"/$f usr/share/factory/etc/
  done
  touch etc/arch-release
  install -m644 "$srcdir"/locale.sh etc/profile.d/locale.sh
  install -Dm644 "$srcdir"/os-release usr/lib/os-release

  # setup /var
  for d in cache local opt log/old lib/misc empty; do
    install -d -m755 var/$d
  done
  install -d -m1777 var/{tmp,spool/mail}

  # allow setgid games (gid 50) to write scores
  install -d -m775 -g 50 var/games
  ln -s spool/mail var/mail
  ln -s ../run var/run
  ln -s ../run/lock var/lock

  # setup /usr hierarchy
  for d in bin include lib share/{misc,pixmaps} src; do
    install -d -m755 usr/$d
  done
  for d in {1..8}; do
    install -d -m755 usr/share/man/man$d
  done

  # add lib symlinks
  ln -s usr/lib lib
  [[ $CARCH = 'x86_64' ]] && {
    ln -s usr/lib lib64
    ln -s lib usr/lib64
  }

  # add bin symlinks
  ln -s usr/bin bin
  ln -s usr/bin sbin
  ln -s bin usr/sbin

  # setup /usr/local hierarchy
  for d in bin etc games include lib man sbin share src; do
    install -d -m755 usr/local/$d
  done
  ln -s ../man usr/local/share/man

  # setup systemd-sysctl
  install -D -m644 "$srcdir"/sysctl usr/lib/sysctl.d/10-arch.conf

  # setup systemd-sysusers
  install -D -m644 "$srcdir"/sysusers usr/lib/sysusers.d/arch.conf

  # setup systemd-tmpfiles
  install -D -m644 "$srcdir"/tmpfiles usr/lib/tmpfiles.d/arch.conf

  # setup systemd.environment-generator
  install -D -m755 "$srcdir"/env-generator usr/lib/systemd/system-environment-generators/10-arch

  # add logo
  install -D -m644 "$srcdir"/avdanos-logo{.png,.svg,-text.svg,-text-dark.svg} usr/share/pixmaps
}

# vim:set ts=2 sw=2 et:
