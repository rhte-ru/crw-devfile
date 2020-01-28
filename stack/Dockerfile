FROM rhel8
#FROM registry.access.redhat.com/ubi8/ubi

RUN \
    FASM_U=https://flatassembler.net/fasm-1.73.21.tgz ; \
    FASM_A=/tmp/fasm.tar.gz ; \
    
    curl -o $FASM_A $FASM_U && \
    dnf install -y \
	bzip2 \
	edk2-ovmf \
	git \
	make \
	mc \
	patch \
	tar \
	zip \
    && \
    tar --strip-components 1 -xzf $FASM_A -C /usr/local/bin \
	fasm{,.x64}
