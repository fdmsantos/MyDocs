# Know Errors

## Unable to lock the administration directory

**Desc:** Unable to lock the administration directory (/var/lib/dpkg/) is another process using it? [Solution](https://askubuntu.com/questions/15433/unable-to-lock-the-administration-directory-var-lib-dpkg-is-another-process)

```bash
sudo rm /var/lib/apt/lists/lock
```