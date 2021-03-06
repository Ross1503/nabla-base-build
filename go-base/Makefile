# Copyright (c) 2018 Contributors as noted in the AUTHORS file
#
# Permission to use, copy, modify, and/or distribute this software
# for any purpose with or without fee is hereby granted, provided
# that the above copyright notice and this permission notice appear
# in all copies.
#
# THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL
# WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED
# WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE
# AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR
# CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS
# OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT,
# NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN
# CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.

export TOP=$(abspath ..)
all: build_docker

RUMPPKGS=$(TOP)/rumprun-packages
RUMPRUN=$(TOP)/rumprun
GORUMP=$(TOP)/gorump
GOBASE=$(TOP)/go-base
GOBOOTSTRAP=$(TOP)/gobootstrap
export GOROOT=$(GOBOOTSTRAP)
unexport GOPATH
unexport GOBIN

include ../Makefile.inc

FILES=\
Dockerfile \
Makefile \

.PHONY: gobootstrap
gobootstrap: FORCE
	rm -rf $(GOBOOTSTRAP)
	mkdir $(GOBOOTSTRAP)
	curl -L https://storage.googleapis.com/golang/go1.10.1.linux-arm64.tar.gz | tar xz -C $(GOBOOTSTRAP) --strip-components=1 

.PHONY: gorump
gorump: gorump_stamp

gorump_stamp: gobootstrap rumprun
	source $(RUMPRUN)/obj/config && source $(RUMPRUN)/obj/config-PATH.sh && \
		cd $(GORUMP)/go/src && CGO_ENABLED=0 GOROOT_BOOTSTRAP=$(GOBOOTSTRAP) GOOS=rumprun GOARCH=amd64 ./make.bash && cd $(GOBASE)
	cp -r $(GORUMP)/go1.5 gorump
	cp $(GORUMP)/go1.5/bin/go go
	touch $@

build_docker: submodule_warning $(FILES) gorump
	cp -r $(RUMPRUN) rumprun-solo5
	sudo docker build --build-arg host_rumproot=$(realpath ../rumprun) -f Dockerfile -t nabla-go-base .
	sudo docker tag nabla-go-base nablact/nabla-go-base
	rm -rf gobootstrap gorump rumprun-solo5

clean:
	rm -rf gobootstrap gorump rumprun-solo5 *_stamp

distclean: clean rumprun-clean
