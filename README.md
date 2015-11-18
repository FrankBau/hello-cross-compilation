# README #

## What is this repository for? ##

* a minimalistic "hello world" project which can be compiled and cross-compiled on many platforms

## How do I get set up? ##

### Simple native compilation ###

Clone the git repo using 

    git clone https://github.com/FrankBau/hello-cross-compilation.git

on your machine.

Open a shell (bash), navigate to the `hello-cross-compilation` folder and enter:

    ./autogen.sh
    ./configure 
    make

For a real-world project, the last step is often a system wide installation which is omitted for this toy project:

    sudo make install

### Cross compilation ###

Let's suppose that we are building on a Linux x86_64 PC for an embedded ARM board. 
For testing, a Yocto project for the MarS Board was used, see https://github.com/FrankBau/meta-marsboard-bsp.

The first step, `autogen`, is executed as usual. Then, the file name of the cross-compiler together with some of its core options is 
defined for `CC` and exported. Note the `--sysroot=` option which points to a folder where the include files and libraries for the embedded ARM board are.
Next the `PATH` environment variable is extended to find the cross compiler.

For cross-compilation, `configure` is called with two options: 
`--build=` the architecture of the Linux PC used for cross-compilation, and 
`--host=` the architecture of the embedded ARM board:

    ./autogen.sh
    export "CC=arm-poky-linux-gnueabi-gcc -march=armv7-a -mthumb -mthumb-interwork -mfloat-abi=hard -mfpu=neon -mtune=cortex-a9 --sysroot=/home/frank/MarSBoard/fido/build/tmp/sysroots/marsboard"
    export PATH=$PATH:/home/frank/MarSBoard/fido/build/tmp/sysroots/x86_64-linux/usr/bin/arm-poky-linux-gnueabi/
    ./configure --build=x86_64-pc-linux-gnu --host=arm-poky-linux-gnueabi
    make

Finding the correct compiler options and `--build` and `--host` strings can be somewhat tricky. 
In our example, the cross-compiling toolchain was created by a Yocto bitbake command, see 
http://www.yoctoproject.org/docs/latest/adt-manual/adt-manual.html#optionally-building-a-toolchain-installer and then installed on the Linux PC.

Note also, that there are much more environment variables besides `CC` which may be or must be specified for more complex projects. 
When using Yocto/bitbake, you may open a devshell (`bitbake -c devshell core-image-minimal-marsboard`) and check the environment
like this:

    printenv | grep arm-angstrom-linux-gnueabi
 
Frank

