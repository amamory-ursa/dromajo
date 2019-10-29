
# Instructions to create some tests for dromajo

## Baremetal riscv-tests

```
git clone https://github.com/riscv/riscv-tests
cd riscv-tests
git submodule update --init --recursive
autoconf
./configure --prefix=${PWD}/../riscv-tests-root/
make
make install
```

To run one of the benchmarks with trace enabled

```
../src/dromajo --trace 0 riscv-tests-root/share/riscv-tests/isa/rv64ua-p-amoadd_d
```

## Linux with buildroot

### Get a trivial buildroot

```
wget https://github.com/buildroot/buildroot/archive/2019.08.1.tar.gz
tar xzvf 2019.08.1.tar.gz
cp config-buildroot-2019.08.1 buildroot-2019.08.01/.config
cd buildroot-2019.08.01
make -j16
```


### Get the Linux kernel up and running

```
https://github.com/torvalds/linux/archive/v5.3.tar.gz
tar xzvf v5.3.tar.gz
cp config-linux-5.3 linux-5.3/.config
cd linux-5.3
make -j16 ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu-
```

### openSBI

```
export CROSS_COMPILE=riscv64-unknown-elf-
wget https://github.com/riscv/opensbi/archive/v0.5.tar.gz
tar xzvf v0.5.tar.gz
tar xzCf opensbi-0.5 opensbi.dromajo.tar.gz
cd opensbi-0.5
make PLATFORM=dromajo FW_PAYLOAD_PATH=../linux-5.3/arch/riscv/boot/Image
```

### To boot Linux (login:root password:root)

```
cp opensbi-0.5/build/platform/dromajo/firmware/fw_payload.bin .
../src/dromajo ./boot.cfg
```

### To boot a 4 multicore

```
../src/dromajo --ncpus 4 ./boot.cfg
```
