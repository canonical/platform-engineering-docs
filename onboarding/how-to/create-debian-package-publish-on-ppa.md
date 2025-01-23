# Create a Debian package and publish it on Platform Engineering's PPA
**Source: [Discourse](https://discourse.canonical.com/t/create-a-debian-package-and-publish-it-on-is-devopss-ppa/3204)**

!!! Still a WIP

This document will talk about how to bootstrap a Debian package repo, what are its components, what they do and how to build, test, sign and publish a Debian package. https://github.com/canonical/jenkins-agent-deb will be used as an example.

This document is based on https://wiki.debian.org/Packaging/Intro and goes more into details about the packaging process. It also tries to explain some hidden convention in the packaging process and put the packaging into the context of the Platform Engineering team.

## Install necessary packages
```
sudo apt install build-essential devscripts debhelper
```

## Bootstrap the package repo
1. `debian/changelog`
```
dch --create -v 1.0-0 --package jenkins-agent
```
2. `debian/control`
Contains information about your package as well as version of the underlying system used for packaging. Example of `debian/control` for jenkins-agent
```
Source: jenkins-agent
Section: java
Priority: optional
Maintainer: Phan Trung Thanh <trung.thanh.phan@canonical.com>
Build-Depends: debhelper-compat (= 13)
Standards-Version: 4.6.1

Package: jenkins-agent
Architecture: any
Depends: ${shlibs:Depends}, ${misc:Depends}
Description: Minimal jenkins agent package.
  Available as a standalone binary or a systemd service
```
3. `\<package\>.service`
If the package ships a `systemd` service, creating a file with this naming convention registers a `systemd` service when the package is installed
```
# File debian/jenkins-agent.service
[Unit]
Description=Jenkins inbound agent

[Service]
Type=simple
ExecStart=/usr/bin/jenkins-agent
ExecStopPost=rm -rf /var/lib/jenkins/.ready

[Install]
WantedBy=multi-user.target
```

4. `\<package\>.install`
Specify how files will be installed in the host's system. For Jenkins agent we'd want a binary to be available in `/usr/bin/jenkins-agent`
```
jenkins-agent /usr/bin
```

5. `debian/rules`
Specifies how the package are built, used as a convenient wrapper for `dh`. For `jenkins-agent` we'd want the `systemd` service to be disabled on start 
```
# File debian/rules
#!/usr/bin/make -f

DPKG_EXPORT_BUILDFLAGS=1

%:
	dh $@

override_dh_installinit:
	dh_installinit --name=jenkins-agent

override_dh_installsystemd:
	dh_installsystemd --no-start
```

## Add to changelog for a new version of the package
```
dch -i <new_version>
```

## Test your package locally
```
debuild -i -us -uc -b
sudo dpkg -i ../jenkins-agent_1.0.5_amd64.deb
```

## Build sources, sign and upload
```
debuild -i -us -uc -S
debsign -k <gpg_key_id> ../jenkins-agent_1.0.5_source.changes
dput ppa:canonical-is-devops/<ppa_name> ../jenkins-agent_1.0.5_source.changes
```


