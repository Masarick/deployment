# This file is subject to the terms and conditions defined in
# 'LICENSE.txt', which is part of this source code distribution.
#
# Copyright 2012-2016 Software Assurance Marketplace

DIRS=SOURCES RPMS SPECS BUILD
RELEASE_NUMBER ?= 0.13
BUILD_NUMBER ?= dev

CP=/bin/cp -p
ifeq ($(shell uname), Darwin) 
TAR=gnutar
else
TAR=tar
endif

TARBALL=SOURCES/runtime.tar.gz

rpm: dirs $(TARBALL) 
	/bin/rm -rf RPMS/noarch
	RELEASE_NUMBER=$(RELEASE_NUMBER) BUILD_NUMBER=$(BUILD_NUMBER) TOPDIR="$(PWD)" make -C SPECS  
#	rm -rf BUILD/*
#	rm -rf SOURCES/*.tar SOURCES/*.gz SOURCES/*.tgz

.PHONY dirs:

$(TARBALL): SOURCES/perlbrew.tgz SOURCES/jre.tar 
	$(TAR) -C SOURCES -czvf $@ perlbrew.tgz jre.tar 

dirs: $(DIRS)

SOURCES/perlbrew.tgz:
	$(CP) -r ../../perl/perlbrew_exec_w-5.18.1.tar.gz SOURCES/perlbrew.tgz

SOURCES/jre.tar:
	$(TAR) -C SOURCES --exclude='.git' --exclude='.svn' -cvf $@ ../../../jdk1.8.0_45/linux/jre 
	
RPMS:
	mkdir -p $@
SPECS:
	mkdir -p $@
SOURCES:
	mkdir -p $@
BUILD:
	mkdir -p $@

clean:
	rm -rf SOURCES BUILD BUILDROOT RPMS *.tap

java: dirs SOURCES/runtime.tar.gz
	/bin/rm -rf RPMS/noarch
	RELEASE_NUMBER=$(RELEASE_NUMBER) BUILD_NUMBER=$(BUILD_NUMBER) TOPDIR="$(PWD)" make -C SPECS  java

perl: dirs SOURCES/runtime.tar.gz
	/bin/rm -rf RPMS/noarch
	RELEASE_NUMBER=$(RELEASE_NUMBER) BUILD_NUMBER=$(BUILD_NUMBER) TOPDIR="$(PWD)" make -C SPECS  perl

# Need to remove swamp-rt-perl, swamp-rt-java first, then put them back
tests:
	-sudo rpm -e swamp-rt-perl
	/usr/local/bin/bats --tap testrtperl   > runtime-perl.tap
	-sudo rpm -i RPMS/noarch/swamp-rt-perl-0-B.noarch.rpm

	-sudo rpm -e swamp-rt-java
	/usr/local/bin/bats --tap testrtjava   > runtime-java.tap
	-sudo rpm -i RPMS/noarch/swamp-rt-java-0-B.noarch.rpm
