## Packages

[Packages](https://archive.archlinux.org/packages/)

## Update Package

```sh
sudo pacman -U <link - see above>
```

## Update corrupted or invalid database

```sh
pacman-key --delete 91FFE0700E80619CEB73235CA88E23E377514E00
pacman-key --populate archlinux
```

## Resolve ICU Package Problem 

[Link](https://github.com/gcarq/inox-patchset/issues/159)

```sh
Download and Install old "icu" package;
 wget https://archive.archlinux.org/packages/i/icu/icu-62.1-1-x86_64.pkg.tar.xz
 sudo pacman -U icu-62.1-1-x86_64.pkg.tar.xz

Copy all "icu" files to a backup directory;
 sudo mkdir /usr/lib/backup
 sudo cp -r /usr/lib/libicu* /usr/lib/backup/

Install new version of "icu" again;
 sudo pacman -U /var/cache/pacman/pkg/icu-62.1-1-x86_64.pkg.tar.xz

Copy this three files back to /var/lib/ direcotry;
 sudo cp /var/lib/libicui18n.so.61 /usr/lib/
 sudo cp /var/lib/libicuuc.so.61 /usr/lib/
 sudo cp /var/lib/libicudata.so.61 /usr/lib/

You can now cleanup backup files;
 sudo rm -rf /var/lib/backup
```