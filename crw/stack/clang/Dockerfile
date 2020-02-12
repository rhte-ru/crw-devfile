#FROM rhel8
FROM registry.access.redhat.com/ubi8/ubi

#--enablerepo codeready-builder-for-rhel-8-x86_64-rpms
RUN \
    dnf install -y --nodocs --disablerepo \* --enablerepo rhel-8-for-x86_64-appstream-rpms --enablerepo rhel-8-for-x86_64-baseos-rpms \
	bzip2 \
	clang \
	diffutils \
	file \
	git \
	libuuid-devel \
	lld \
	make \
	patch \
	tar \
	uuid \
	zip && \
    adduser -u 1001 developer

ENV HOME /home/developer

ENV SRC $HOME/src

RUN \
    FASM_U=https://flatassembler.net/fasm-1.73.21.tgz ; \
    FASM_A=/tmp/fasm.tar.gz ; \
    curl -o $FASM_A $FASM_U && \
    tar --strip-components 1 -xzf $FASM_A -C /usr/local/bin \
	fasm/fasm{,.x64} && \
    cd /usr/local/bin && \
    mv fasm{,.x86} && \
    ln -s fasm{.x64,} ; \
    rm -f $FASM_A
    

USER 1001

RUN \
    mkdir -p $SRC

WORKDIR $SRC

RUN \
    git clone https://github.com/yoppeh/efi-clang.git hello ; \
    git clone https://github.com/yoppeh/efi.git && \
    sed -ci.bak1 's|-I efi|-I ../efi|' hello/makefile
