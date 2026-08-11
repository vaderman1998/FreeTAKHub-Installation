![CI](https://github.com/FreeTAKTeam/FreeTAKHub-Installation/actions/workflows/zerotouch.yml/badge.svg)

# About this fork (`fts-fixed-branch`)

This fork installs FreeTAKServer from the maintained fix branch
[vaderman1998/FreeTakServer@fix/broken-out-of-box-startup](https://github.com/vaderman1998/FreeTakServer/tree/fix/broken-out-of-box-startup)
instead of PyPI. Upstream FreeTAKServer 2.2.1 on PyPI does not start out of
the box (missing `requests` dependency, broken `opentelemetry-sdk` pin) and
pins a 2022-era crypto stack (cryptography 36 / pyOpenSSL 22, bundling EOL
OpenSSL 1.1.1). The fix branch repairs startup, ports certificate generation
to modern `cryptography`, regenerates the protobuf gencode, and makes the
server co-installable with FreeTAKServer-UI.

Behavior is controlled by `fts_git_requirement` in
`roles/freetakserver/defaults/main.yml`; set it to `""` to restore the
original PyPI install.

This fork also supports **Ubuntu 24.04 (noble)** in addition to 22.04:
Python 3.11 is installed from the deadsnakes PPA (noble's archive only
carries 3.12, which the FTS stack has not been validated on), and the
`python3-distutils` package (removed in noble) is skipped there.

Run the installer from this fork with (`CORE=true` installs server + web UI;
omit it to also install Murmur, the video server and Node-RED, which are
untested on 24.04):

```bash
wget -qO /tmp/easy_install.sh https://raw.githubusercontent.com/vaderman1998/FreeTAKHub-Installation/fts-fixed-branch/scripts/easy_install.sh
sudo CORE=true REPO=https://github.com/vaderman1998/FreeTAKHub-Installation.git BRANCH=fts-fixed-branch bash /tmp/easy_install.sh
```

The server address baked into client packages and the web UI defaults to the
host's primary LAN IPv4. Installing on a cloud host (or anywhere clients
connect via a different address)? Pass it explicitly:
`sudo FTS_IP_CUSTOM=<address> CORE=true REPO=... BRANCH=... bash /tmp/easy_install.sh`

Post-install, rotate the default credentials before exposing the server:
the seeded admin user (`admin`/`password`, API token `token`), the
websocket key (`YourWebsocketKey`), and the UI Flask `SECRET_KEY` are all
well-known defaults.

---

This page is for developers of the Zero Touch Installer for [FreeTAKServer](https://github.com/FreeTAKTeam/FreeTakServer).
Please refer to the [official documentation ](https://freetakteam.github.io/FreeTAKServer-User-Docs/) for usage.

# Configuring the Development Environment

## Cloning the Repository

Clone the origin repository.
The following is the official repository.
```bash
git clone --origin upstream https://github.com/FreeTAKTeam/FreeTAKHub-Installation.git ${HOME}/fth-install
```

You will want to commit your work into a fork of the repository.
```bash
pushd  ${HOME}/fth-install
git remote add origin <url-of-fork>
```

# Running the ZTI locally

You will need some variant of Ubuntu 22.04 on your development machine.
The following will install FTS on your development machine.
```bash
cat ./scripts/easy_install.sh | sudo bash -s -- --verbose
```

This will install the production repository,
unless you are modifying `scripts/easy_install.sh` you will want your cloned repository.

The following will remove any previously retrieved repository replacing it with a clone of the provided one.
```bash
pushd  ${HOME}/fth-install
cat ./scripts/easy_install.sh | sudo bash -s -- --verbose --repo file://$(pwd)/.git
```

So long as you are working with the same git repository the `--repo` option could (and should)
be omitted from subsequent runs as the default is to reuse the clone.


## Regression Testing the ZTI

The ZTI is officially supported on the following platforms:

* Raspberry Pi
* [Ubuntu Server](docs/ubuntu_vm_test.md)
* Digital Ocean Cloud




