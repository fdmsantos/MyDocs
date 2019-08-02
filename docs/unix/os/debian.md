# Know Errors (Ubuntu)

## Unable to lock the administration directory

**Desc:** Unable to lock the administration directory (/var/lib/dpkg/) is another process using it? [Solution](https://askubuntu.com/questions/15433/unable-to-lock-the-administration-directory-var-lib-dpkg-is-another-process)

```bash
sudo rm /var/lib/apt/lists/lock
```

# dpkg

```bash
# To list all installed packages
dpkg -l | less

# To install package
sudo dpkg -i dlocate_1.02+nmu3_all.deb

# To Remove
sudo dpkg --purge dlocate
```

# Apt

```bash
# Sources file
cat /etc/apt/sources.list
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade # Better
# To Get dependences
apt-cache depends apache2 | less

```