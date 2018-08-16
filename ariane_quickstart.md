# Quickstart Guide to Ariane
This guide shows how anyone can quickstart the [64-bit ariane RISC-V core](https://github.com/pulp-platform/ariane) on linux. We will run a simulation of the core using [verilator](https://www.veripool.org/wiki/verilator), an open-source verilog simulator. 

## Install Verilator

### From Source

This is the prefered way to install verilator since it will install the most current one. We will install the current stable release.

0. Get the requirements to build verilator. 

    Ubuntu:
    ```console
    $ sudo apt install git make autoconf g++ flex bison
    ```

    Arch:
    ```console
    $ sudo pacman -S git make autoconf gcc flex bison
    ```

    Fedora:
    ```console
    $ sudo dnf install git make autoconf gcc-c++ flex bison
    ```

1. Clone the repository and cd into the directory
    ```console
    $ git clone http://git.veripool.org/git/verilator
    $ cd verilator
    ```

2. Checkout the current stable release
    ```console
    $ git checkout stable
    ```

3. Compile
    ```console
    $ autoconf
    $ ./configure
    $ make
    ```

4. Install
    ```console
    $ sudo make install
    ```

### Package Manager

On most distributions there is already a package containing verilator. However most distributions ship old versions. The prefered way to install verilator is form source.

Debian/Ubuntu
```console
$ sudo apt install verilator
```

Arch linux
```console
$ sudo pacman -S verilator
```

Fedora
```console
$ sudo dnf install verilator
```

## Install the RISC-V Tools

The RISC-V tools include the entire toolchain for the RISC-V instruction set and it will take up some space on your disk (installed size: ~800mb, repository size: __~7gb__).

__Warning:__ This will take a while.

0. Get the requirements

    Ubuntu:
    ```console
    $ sudo apt-get install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev libusb-1.0-0-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev device-tree-compiler pkg-config libexpat-dev
    ```

    Arch:
    ```console
    $ sudo pacman -S autoconf automake curl libmpc mpfr gmp libusb gawk bison flex texinfo gperf libtool patchutils bc zlib expat
    ```

    Fedora
    ```console
    $ sudo dnf install autoconf automake @development-tools curl dtc libmpc-devel mpfr-devel gmp-devel libusb-devel gawk gcc-c++ bison flex texinfo gperf libtool patchutils bc zlib-devel expat-devel
    ```

1. Clone the repository and all its submodules
    ```console
    $ git clone https://github.com/riscv/riscv-tools.git
    $ cd riscv-tools
    $ git submodule update --init --recursive
    ```

2. Set the $RISCV environment variable to the path where you want to install the RISCV-tools and add that to your path (preferably permanent in `.bashrc` or `.zshrc`). Example:
    ```console
    $ export RISCV=/opt/riscv
    $ export PATH=$PATH:$RISCV/bin
    ```

3. Run the build script. For faster compile times set `$MAKEFLAGS` to the number of cores.
    ```console
    $ ./build.sh
    ```

## Install patched `riscv-fesvr`

Ariane requires a forked version of `riscv-fesvr`.

```console
$ git clone https://github.com/pulp-platform/riscv-fesvr.git
$ cd riscv-fesvr
$ mkdir build
$ cd build
$ ../configure --prefix=$RISCV
$ make install
```

## Ariane

Finally we can clonse the ariane repository and simulate `riscv-tests` on the core.

1. Clone ariane
    ```console
    $ git clone git@github.com:pulp-platform/ariane.git
    $ cd ariane
    ```

2. Compile and run all of `riscv-tests` using verilator
    ```console
    $ make verilate
    $ make run-asm-tests-verilator
    ```