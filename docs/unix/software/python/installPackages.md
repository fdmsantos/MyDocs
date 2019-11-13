# Install With No Internet Access and No Root

## With no PIP

```bash
export PYTHONPATH=~/lib/python
# Download and upload package tar.gz
tar -xvz package-name.tar.gz
cd package-name
python3.6 setup.py install --home ~
```

## With PIP

* This process install all dependencies

https://stackoverflow.com/questions/36725843/installing-python-packages-without-internet-and-using-source-code-as-tar-gz-and


```bash
# Machine with Internet Access
# Use Docker to have environment similiar to target machine
mkdir keystone-deps
pip download python-keystoneclient -d "/home/aviuser/keystone-deps"
tar cvfz keystone-deps.tgz keystone-deps

# Machine to install Package
tar xvfz keystone-deps.tgz
cd keystone-deps
pip install --target=~/lib/python keystone-deps.whl -f ./ --no-index
```

## With PIP but not installed

https://stackoverflow.com/questions/36132350/install-python-wheel-file-without-using-pip


