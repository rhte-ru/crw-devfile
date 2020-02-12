#FROM rhel8
FROM registry.access.redhat.com/ubi8/ubi

RUN \
    dnf install -y --nodocs --disablerepo \* --enablerepo codeready-builder-for-rhel-8-x86_64-rpms --enablerepo rhel-8-for-x86_64-appstream-rpms --enablerepo rhel-8-for-x86_64-baseos-rpms \
	bzip2 \
	edk2-ovmf \
	file \
	git \
	gcc \
	gcc-c++ \
	iasl \
	libuuid-devel.x86_64 \
	make \
	mc \
	nasm \
	patch \
	python36 \
	tar \
	uuid \
	zip && \
    ln -s /usr/bin/python{3,} && \
    adduser -u 1001 developer

RUN \
    FASM_U=https://flatassembler.net/fasm-1.73.21.tgz ; \
    FASM_A=/tmp/fasm.tar.gz ; \
    curl -o $FASM_A $FASM_U && \
    tar --strip-components 1 -xzf $FASM_A -C /usr/local/bin \
	fasm/fasm{,.x64} && \
    cd /usr/local/bin ; \
    mv fasm{,.x86} ; \
    ln -s fasm{.x64,} ; \
    rm -f $FASM_A

USER 1001

ENV HOME /home/developer

ENV SRC $HOME/src

ENV EDK_HOME $SRC/edk2

RUN \
    mkdir -p $SRC && \
    git clone git://github.com/tianocore/edk2.git $EDK_HOME

WORKDIR $EDK_HOME

RUN \
    make -C BaseTools

#ENV EDK_TOOLS_PATH /opt/tianocore/edk2

RUN \
    bash -c ' . edksetup.sh BaseTools' && \
    sed -ci.bak1 Conf/target.txt -e 's/ACTIVE_PLATFORM.*/ACTIVE_PLATFORM = MdeModulePkg\/MdeModulePkg.dsc/ ; s/TOOL_CHAIN_TAG.*/TOOL_CHAIN_TAG = GCC49/ ; s/TARGET_ARCH.*/TARGET_ARCH = IA32 X64/ ;' && \
    bash -c ' . edksetup.sh && build '

#RUN \
#    WIZARD_HOME=$SRC/udw ; \
#    WIZARD_U=https://github.com/tianocore/edk2-share ; \
#    git clone $WIZARD_U $WIZARD_HOME
