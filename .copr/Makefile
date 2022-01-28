.PHONY: installdeps installprevious srpm

installdeps:
	dnf -y install git jq wget rpmlint nodejs dnf-plugins-core

# explicity mark the copr generated git repo directory (which is done prior to the mock
# call to the make_srpm and will be the current pwd) as safe for git commands
git-safe:
	git config --global --add safe.directory "$(shell pwd)"

# To support "cache prefill" when generating the srpm, the previous build of the
# package needs to be installed.  With `dnf-plugins-core` installed, we can enable
# the proper copr repo for the source build chroot.  Also, since the source build
# chroot can change fedora versions, specify the copr chroot to centos-stream-8 (distro
# and arch isn't that important since the package is noarch).
#
# Installing the previous version vastly reduces the number of packages that need to be
# fetched from the yarn/npm repos and thereby keeps the builds away from rate limiting
# by those services.
installprevious:
	dnf -y copr enable ovirt/ovirt-master-snapshot centos-stream-8-x86_64
	dnf -y install ovirt-engine-nodejs-modules

srpm: installdeps git-safe installprevious
	./automation/build.sh copr
	cp exported-artifacts/*.src.rpm $(outdir)
