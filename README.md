[![Build Status](https://builds.ixsystems.com/jenkins/job/TrueOS%20-%20Snapshot%20Build/badge/icon)](https://builds.ixsystems.com/jenkins/job/TrueOS%20-%20Snapshot%20Build/)

TrueOS Source:
--------------

This is the top level TrueOS source directory. It is more or less a fork
of [FreeBSD](https://github.com/freebsd/freebsd).

The last sync with FreeBSD was on Oct 26th 2018, with the commit [d119e0c8d0fa3f99c61875e4d38f94f2ed75d5c2](https://github.com/freebsd/freebsd/commit/d119e0c8d0fa3f99c61875e4d38f94f2ed75d5c2).

TrueOS Differences:
--------------

In what ways does TrueOS differ from stock FreeBSD you may be wondering?
Read on for a list of the distinctions in no particular order:

* Bi-Annual Release Cycle - TrueOS follows traditional FreeBSD HEAD and cuts new releases
on a 6 months schedule which includes OS and Ports.

* GitHub - TrueOS uses Git/GitHub as the "Source of truth", Pull-Requests welcome!

* CI - Our [Jenkins build cluster](https://builds.ixsystems.com/jenkins/job/TrueOS%20-%20World%20CI/) is constantly building [new versions](https://builds.ixsystems.com/jenkins/job/TrueOS%20-%20World%20CI/job/trueos-master/) of TrueOS for testing.

* [dhcpcd](https://github.com/rsmarples/dhcpcd) - dhcpcd has been integrated directly into base,
allowing more advanced IPv4 and IPv6 network configuration

* [OpenRC](https://github.com/openrc/openrc/) - This replaces the legacy rc.d scripts with
OpenRC's init.d subsystem, allowing faster boots, as well as a host of other service improvements.

* Package Base - TrueOS is installed and based on using Packages for the Base OS.

* [pkg in base](https://github.com/freebsd/pkg) - To go along with using base system packages,
TrueOS has also integrated PKG directly in the base system.

* Root NSS Certs - Since it really is a bummer to not be able to use HTTPS out of box...

* Custom Installer - TrueOS includes its own [pc-sysinstall](https://github.com/trueos/trueos/tree/trueos-master/usr.sbin/pc-sysinstall) installation system, along with
[text-based](https://github.com/trueos/trueos/tree/trueos-master/usr.sbin/pc-installdialog) front-end. This allows a wide variety of ZFS-based installation options, as well
as scriptability.

* [JSON Build Manifest](https://github.com/trueos/trueos/tree/trueos-master/release/README.md) - TrueOS supports a customizable JSON manifest for building. This allows TrueOS to run poudriere and assemble installation images for a variety of use-cases.

* Single repo - Base packages and ports now share a single repository which allows base packages to depend upon ports, such as llvm60 or jq for -devel packages

* Clang disabled from base - This allows us to buildworld in a fraction of the time, while using a more up to date version of clang/llvm from ports

* More as they come...

Build Instructions:
--------------
The following instructions may be used to generate TrueOS installation
images:

```
make buildworld buildkernel
make packages
cd release && make iso
```
If you want to re-use the base packages and just re-run port builds, you may do so with:
```
make ports
```

To change the default manifest from CI to something else, set the TRUEOS_MANIFEST variable to the full path. I.E:

```
# setenv TRUEOS_MANIFEST /usr/src/release/manifests/trueos-master.json
# make buildworld buildkernel
 ...
```

Compiler Instructions:
--------------

By default TrueOS now uses the external llvm compiler from ports. This will be included with "TrueNAS-runtime-development" when installed.

To change from the default (current llvm60) you can run:

```
# compiler-bootstrap llvm70
```

Currently only llvm60 and llvm70 are supported. If you have both installed you can toggle between them by re-running the
"compiler-bootstrap" command as necessary.

Using Poudriere:
--------------

Since TrueOS uses an external toolchain, an additional step is required before using stock Poudriere. This is to install the base
pkg environment and boot-strap the external compiler. To create a Poudriere jail on TrueOS, run the following:

```
# mk-base-jail -t poudriere -c llvm60 <directory>
```

You can then import this prepared jail into poudriere using the following command:

```
# poudriere jail -c -j myjailname -m null -M <directory> -v 12.0-RELEASE
```

Replace "myjailname" and 12.0-RELEASE" as necessary.
