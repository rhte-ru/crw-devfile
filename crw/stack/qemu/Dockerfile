#FROM rhel8
FROM registry.access.redhat.com/ubi8/ubi

RUN \
    dnf install -y --nodocs --disablerepo \* --enablerepo codeready-builder-for-rhel-8-x86_64-rpms --enablerepo rhel-8-for-x86_64-appstream-rpms --enablerepo rhel-8-for-x86_64-baseos-rpms \
	libguestfs-tools-c \
	mtools \
	qemu-img

RUN \
    adduser -u 1001 developer

ENV HOME /disk

WORKDIR $HOME

COPY examples/*.efi /tmp/

RUN \
    set -x ; \
    n=640 ; \
    export SZ=$(expr $n \* 1024 \* 1024) ; \
    export SC=$(expr $SZ / 512); \
    echo "size=$SZ ; sectors=$SC" ; \
    GPT_SC=2048 ; \
    dd if=/dev/zero of=$HOME/uefi.img bs=512 count=$(expr $SC + 2 \* $GPT_SC) && \
    parted $HOME/uefi.img -s -a minimal mktable gpt && \
    parted $HOME/uefi.img -s -a minimal mkpart EFI FAT32 ${GPT_SC}s ${SC}s && \
    parted $HOME/uefi.img -s -a minimal toggle 1 boot && \
    dd if=/dev/zero of=$HOME/part1.img bs=512 count=${SC} && \
    mkfs.fat -F 32 $HOME/part1.img && \
    echo "mtools_skip_check=1" > ~/.mtoolsrc && \
    mcopy -i $HOME/part1.img /tmp/*.efi :: && \
    dd if=$HOME/part1.img of=$HOME/uefi.img bs=512 count=${SC} seek=2048 conv=notrunc && \
    qemu-img convert -O qcow2 $HOME/uefi.{img,qcow2} && \
    rm -f $HOME/*.img && \
    set +x

RUN \
    set -x ; \
    n=640 ; \
    export SZ=$(expr $n \* 1024 \* 1024) ; \
    export SC=$(expr $SZ / 512); \
    echo "size=$SZ ; sectors=$SC" ; \
    GPT_SC=2048 ; \
    fallocate -x -l $(expr $SZ + 2 \* $GPT_SC \* 512) $HOME/fuefi.img && \
    parted $HOME/fuefi.img -s -a minimal mktable gpt && \
    parted $HOME/fuefi.img -s -a minimal mkpart EFI FAT32 ${GPT_SC}s ${SC}s && \
    parted $HOME/fuefi.img -s -a minimal toggle 1 boot && \
    fallocate -x -l $SZ $HOME/part1.img && \
    mkfs.fat -F 32 $HOME/part1.img && \
    echo "mtools_skip_check=1" > ~/.mtoolsrc && \
    mcopy -i $HOME/part1.img /tmp/*.efi :: && \
    dd if=$HOME/part1.img of=$HOME/fuefi.img bs=512 count=${SC} seek=2048 conv=notrunc && \
    qemu-img convert -O qcow2 $HOME/fuefi.{img,qcow2} && \
    rm -f $HOME/*.img && \
    set +x
