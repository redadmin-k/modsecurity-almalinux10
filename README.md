# ModSecurity RPM for AlmaLinux 10

This repository provides an unofficial RPM package for ModSecurity v3 on AlmaLinux 10.

This package is intended for testing and verification of Nginx WAF integration using the ModSecurity-nginx connector.

## Overview

This RPM installs ModSecurity under:

```text
/opt/modsecurity
```

Example runtime library path:

```text
/opt/modsecurity/lib64/libmodsecurity.so.3
```

The package includes:

- ModSecurity runtime libraries
- ModSecurity headers
- pkg-config files
- example configuration files
- ldconfig configuration for `/opt/modsecurity/lib64`

This package can be used together with an Nginx build that includes the ModSecurity-nginx connector.

## AlmaLinux 10 Dependency Note

On AlmaLinux 10, YAJL is required for building and installing ModSecurity.

This repository expects YAJL to be installed separately.

The YAJL RPMs for AlmaLinux 10 are provided here:

```text
https://github.com/redadmin-k/yajl-almalinux10
```

Install the YAJL packages before building or installing ModSecurity:

```bash
sudo dnf localinstall \
  yajl-2.1.0-25.el10.x86_64.rpm \
  yajl-devel-2.1.0-25.el10.x86_64.rpm
```

For normal ModSecurity usage, the following YAJL packages are required:

```text
yajl-2.1.0-25.el10.x86_64.rpm
yajl-devel-2.1.0-25.el10.x86_64.rpm
```

The debuginfo, debugsource, and source RPM packages are not required for normal installation.

## Repository Layout

```text
README.md
SPEC/
RPMS/
Logs/
```

RPM files are stored under:

```text
RPMS/
```

## Install

First, install the YAJL dependency packages from:

```text
https://github.com/redadmin-k/yajl-almalinux10
```

Then install the built ModSecurity RPM package:

```bash
sudo dnf localinstall RPMS/*.rpm
```

If RPM files are stored under an architecture subdirectory, use:

```bash
sudo dnf localinstall RPMS/x86_64/*.rpm
```

A more general command is:

```bash
sudo dnf localinstall $(find RPMS -type f -name '*.rpm' | sort)
```

## Build Notes

This package requires YAJL development files when building.

The spec file uses:

```spec
BuildRequires:  yajl-devel
Requires:       yajl
```

On AlmaLinux 10, install the YAJL RPMs first:

```bash
sudo dnf localinstall \
  yajl-2.1.0-25.el10.x86_64.rpm \
  yajl-devel-2.1.0-25.el10.x86_64.rpm
```

Additional build tools may be required:

```bash
sudo dnf install \
  rpm-build \
  gcc \
  gcc-c++ \
  make \
  git \
  autoconf \
  automake \
  libtool \
  pkgconf-pkg-config \
  pcre2-devel \
  libxml2-devel \
  curl-devel \
  lmdb-devel \
  zlib-devel \
  patchelf
```

The source archive must be created from a complete ModSecurity git checkout with submodules.

Do not use the small GitHub-generated `v3.0.16.tar.gz` archive directly.

Example source archive creation:

```bash
git clone https://github.com/owasp-modsecurity/ModSecurity.git
cd ModSecurity
git checkout v3.0.16
git submodule update --init --recursive
cd ..
tar --transform 's,^ModSecurity,ModSecurity-3.0.16,' \
  -czf v3.0.16.tar.gz ModSecurity
```

Place the archive under the RPM build source directory before building the SRPM/RPM.

## Verify Installation

Check installed files:

```bash
rpm -ql modsecurity | grep /opt/modsecurity
```

Check that the runtime library is visible to the dynamic linker:

```bash
ldconfig -p | grep libmodsecurity
```

Expected example:

```text
libmodsecurity.so.3 => /opt/modsecurity/lib64/libmodsecurity.so.3
```

## Example Paths

Main installation directory:

```text
/opt/modsecurity
```

Library directory:

```text
/opt/modsecurity/lib64
```

Header directory:

```text
/opt/modsecurity/include
```

pkg-config directory:

```text
/opt/modsecurity/lib64/pkgconfig
```

Example configuration files:

```text
/opt/modsecurity/share/modsecurity.conf-recommended
/opt/modsecurity/share/unicode.mapping
```

ldconfig configuration:

```text
/etc/ld.so.conf.d/modsecurity.conf
```

## Nginx Integration Notes

This package only installs ModSecurity itself.

To use ModSecurity with Nginx, Nginx must be built with the ModSecurity-nginx connector.

When building Nginx, make sure the build can find ModSecurity under:

```text
/opt/modsecurity
```

For example:

```bash
export PKG_CONFIG_PATH=/opt/modsecurity/lib64/pkgconfig
```

## ModSecurity Rule Engine

Before enabling blocking mode in production, it is recommended to run ModSecurity in detection-only mode and review logs carefully.

Example:

```apache
SecRuleEngine DetectionOnly
```

After sufficient verification, blocking mode can be enabled if appropriate.

```apache
SecRuleEngine On
```

## Target Environment

Tested target environment:

```text
AlmaLinux 10
ModSecurity v3
Nginx with ModSecurity-nginx connector
YAJL rebuilt for AlmaLinux 10
```

## Disclaimer

This repository is unofficial.

It is not provided, maintained, endorsed, or supported by AlmaLinux OS Foundation, OWASP, Trustwave, or any upstream project.

Use this repository at your own risk.

The author provides no warranty of any kind.

Please test carefully in a verification environment before using it in production.

## License

ModSecurity is licensed under the Apache License 2.0.

See the upstream ModSecurity project for details:

```text
https://github.com/owasp-modsecurity/ModSecurity
```

Packaging files in this repository are provided for RPM build and integration testing purposes.

Unless otherwise stated, packaging files in this repository are released under the Apache License 2.0.
