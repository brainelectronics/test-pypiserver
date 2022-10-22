# Test PyPi Server

PyPi Server for test deployments of MicroPython packages

---------------

## General

PyPi Server for testing and deploy of MicroPython packages

<!-- MarkdownTOC -->

- [Installation](#installation)
	- [Install required tools](#install-required-tools)
		- [Python](#python)
		- [Docker Compose](#docker-compose)
		- [htpasswd](#htpasswd)
- [Setup](#setup)
	- [Directories](#directories)
	- [Password file](#password-file)
	- [pypirc file](#pypirc-file)
- [Usage](#usage)
	- [Start PyPi server](#start-pypi-server)
	- [Upload](#upload)
	- [Download](#download)
		- [Python](#python-1)
		- [MicroPython](#micropython)
			- [Connect to a network](#connect-to-a-network)
			- [Install latest package version](#install-latest-package-version)
			- [Install specific package version](#install-specific-package-version)
- [Credits](#credits)

<!-- /MarkdownTOC -->

## Installation
### Install required tools
#### Python

Python3 must be installed on your system. Check the current Python version
with the following command

```bash
python --version
python3 --version
```

Depending on which command `Python 3.x.y` (with x.y as some numbers) is
returned, use that command to proceed.

```bash
python3 -m venv .venv
source .venv/bin/activate

pip install -r requirements.txt
```

#### Docker Compose

See [Install Docker Compose][ref-docker-compose-install]

```bash
docker compose version
# Docker Compose version v2.10.2
```

#### htpasswd

See [htpasswd docs][ref-htpasswd-usage]

```bash
sudo apt-get install apache2-utils
```

## Setup
### Directories

Create the required directories to store the uploaded artifacts

```bash
mkdir -p data/auth
mkdir -p data/packages
```

### Password file

```bash
htpasswd -sc data/auth/.htpasswd upload-bot-0

New password: asdf
Re-type new password: asdf
Adding password for user upload-bot-0
```

### pypirc file

This file is used to authenticate the client at the PyPi Server. Edit or create
a `~/.pypirc` file on your system with a similar content

```
[distutils]
index-servers =
  pypi
  mypypiserver

[pypi]
username:<your_pypi_username>
password:<your_pypi_passwd>

[mypypiserver]
repository: http://localhost:8089
username: upload-bot-0
password: asdf
```

## Usage
### Start PyPi server

Run the following command in the directory of the `docker-compose.yml` file

```bash
docker compose up
```

The PyPi server will be available on `http://localhost:8089`. The port `8089`
is choosen as maybe other web servers or services are already running on the
system and using the general default port `8080`.

### Upload

In order to upload an already created distribution file use this command

```bash
# if specified in pypirc file
twine upload --repository mypypiserver path/to/my-custom-package.tar.gz

# use a URL instead of a keyword
twine upload --repository http://localhost:8089 path/to/my-custom-package.tar.gz
```

### Download
#### Python

```bash
# if specified in pypirc file
pip install 'my-custom-package==0.1.0'

# otherwise use the raw URL to the PyPi server
pip install --index-url http://localhost:8089 --trusted-host http://localhost:8089 'my-custom-package==0.1.0'
```

#### MicroPython

To use the PyPi server with MicroPython and it's `upip` package, a specialized
JSON endpoint is used, which is supported by pypiserver to provide the package
informations. The JSON data can be inspected at
`http://localhost:8089/PACKAGE_NAME/json`. It contains similar data as the
following example depending on the IP address running the PyPi server and of
course the uploaded packages.

```JSON
{
  "info": {
    "version": "0.2.0rc99+dev42"
  },
  "releases": {
    "0.2.0rc99+dev42": [
      {
        "url": "http://192.168.178.105:8089/packages/my-micropython-package-0.2.0rc99+dev42.tar.gz"
      }
    ],
    "0.1.0rc1+dev1": [
      {
        "url": "http://192.168.178.105:8089/packages/my-micropython-package-0.1.0rc1+dev1.tar.gz"
      }
    ]
  }
}
```

MicroPython will [get this JSON][ref-upy-get-package-data] and install either
the latest version, taken specified by `['info']['version']` and then looked
up in `['releases'][VERSION]` or from a specific version. In either case the
`url` value of the specified release will be used to download the archive file.

##### Connect to a network

```python
import network
import upip

station = network.WLAN(network.STA_IF)
station.active(True)
station.connect('SSID', 'PASSWORD')
```

##### Install latest package version

To install the latest version of the hosted `my-micropython-package` use
this set of commands on a MicroPython board

```python
upip.index_urls = ["http://IP-ADDRESS-OF-PYPISERVER:8089"]
upip.install("my-micropython-package")
```

Which will produce the following output on the MicroPython board

```
Installing my-micropython-package 0.2.0rc99+dev42 from http://192.168.178.105:8089/packages/my-micropython-package-0.2.0rc99+dev42.tar.gz
```

##### Install specific package version

To install a specific version of the hosted `my-micropython-package` use
this set of commands on a MicroPython board

```python
upip.index_urls = ["http://IP-ADDRESS-OF-PYPISERVER:8089"]
upip.install("my-micropython-package==0.1.0rc1+dev1")
```

Which will produce the following output on the MicroPython board

```
Installing my-micropython-package 0.1.0rc1+dev1 from http://192.168.178.105:8089/packages/my-micropython-package-0.1.0rc1+dev1.tar.gz
```

## Credits

Based on the [PyPiServer instructions][ref-pypiserver].

<!-- Links -->
[ref-docker-compose-install]: https://docs.docker.com/compose/install/
[ref-htpasswd-usage]: https://httpd.apache.org/docs/2.4/programs/htpasswd.html
[ref-upy-get-package-data]: https://github.com/micropython/micropython/blob/da4b38e7562dfa451917f9d7f344a7f26de8c7bd/tools/upip.py#L196
[ref-pypa-pypiserver]: https://github.com/pypiserver/pypiserver
