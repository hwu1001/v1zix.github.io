---
title: Building Zig on Ubuntu 18.04 LTS
date: "2019-06-02T19:19:43.607Z"
template: "post"
draft: false
slug: "/posts/building-zig-on-ubuntu-1804/"
category: "Zig"
tags:
  - "Zig"
  - "ziglang"
  - "Linux"
  - "Ubuntu"
description: "Building Zig from source on Ubuntu 18.04 LTS."
---

I'm writing these instructions after installing Ubuntu 18.04.02 LTS on an older desktop. So it should apply to any fresh install of Ubuntu 18.04.

Note that anything with `$` in front of it means execute the command in your Terminal application.

My output of `$ lsb_release -a`:
```
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.2 LTS
Release:	18.04
Codename:	bionic
```

## Install git
`$ sudo apt install git`

You should be able to run: `$ git --version`

And have your git version appear if the installation is successful.

## Clone the Zig repository

Go to https://github.com/ziglang/zig in your web browser and click on the `Clone or download` button. Copy the URL to your clipboard and back at your terminal:
`$ git clone https://github.com/ziglang/zig.git`

## Install LLVM and related dependencies

This section basically goes over the instructions from here: https://linuxhint.com/install-llvm-ubuntu/

⚠️ Note that the LLVM version will change as Zig and LLVM update. The version Zig targets as I write this is LLVM 8. In the future when LLVM 9 is out that will be the version that Zig eventually targets.

Go to https://apt.llvm.org/ in your web browser and find the links for Ubuntu 18.04 for LLVM 8.

Copy the one beginning with `deb` not `deb-src` onto your clipboard.

On your machine open the Application Menu and search for `update` to find and open the `Software & Updates` program. Once the application opens click on the `Other Software` tab and click on the `Add` button. 

Paste the link into the APT line. Then click on the `Add Source +` button. Enter your password and click the `Authenticate` button. 

Now open a Terminal session (Ctrl + Alt + T on Ubuntu) and run this command to retreive the archive signature:
`$ wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key|sudo apt-key add -`

Once the archive signature retrieval finishes we can update the package repository with this command:
`$ sudo apt-get update`

Output should look something like this:
```
Get:1 http://apt.llvm.org/bionic llvm-toolchain-bionic-8 InRelease [4,231 B]
Get:2 http://apt.llvm.org/bionic llvm-toolchain-bionic-8/main amd64 Packages [8,136 B]
Hit:3 http://us.archive.ubuntu.com/ubuntu bionic InRelease                     
Get:4 http://apt.llvm.org/bionic llvm-toolchain-bionic-8/main i386 Packages [1,573 B]
Get:5 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]    
Get:6 http://us.archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]   
Hit:7 http://us.archive.ubuntu.com/ubuntu bionic-backports InRelease           
Fetched 191 kB in 1s (151 kB/s)                                                
Reading package lists... Done
```

Now we can install LLVM and other tools from https://apt.llvm.org/ with the package manager. I recommend everything under `#LLVM`, `# Clang and co`, `# lldb`, and `# lld (linker)`. Not sure that you need the last two, but I installed `lldb` since it's nice for debugging. LLVM and Clang are definitely required though. Run the following commands to install:

* LLVM
  * `$ sudo apt-get install libllvm-8-ocaml-dev libllvm8 llvm-8 llvm-8-dev llvm-8-doc llvm-8-examples llvm-8-runtime`
* Clang and co
  * `$ sudo apt-get install clang-8 clang-tools-8 clang-8-doc libclang-common-8-dev libclang-8-dev libclang1-8 clang-format-8 python-clang-8`
* lldb
  * `$ sudo apt-get install lldb-8`
* lld (linker)
  * `$ sudo apt-get install lld-8`

## Install cmake and build-essential

`$ sudo apt install cmake`

If you try to build Zig at this point you'll run into this error:
```
$ cmake ..
-- The C compiler identification is GNU 7.4.0
-- The CXX compiler identification is unknown
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
CMake Error at CMakeLists.txt:13 (project):
  No CMAKE_CXX_COMPILER could be found.

  Tell CMake where to find the compiler by setting either the environment
  variable "CXX" or the CMake cache entry CMAKE_CXX_COMPILER to the full path
  to the compiler, or to the compiler name if it is in the PATH.


-- Configuring incomplete, errors occurred!
See also "/home/me/Documents/dev/ziglang/zig/build/CMakeFiles/CMakeOutput.log".
See also "/home/me/Documents/dev/ziglang/zig/build/CMakeFiles/CMakeError.log".
```

I tried setting `CC` and `CXX` to  `/usr/bin/clang-8` for the C and C++ compiler and the `cmake` command succeeds, but `make install` will fail with various libraries not linked or conflicts on types. In any case this resolves the issue:
`$ sudo apt-get install build-essential`

Once I ran the above Zig is built with `/usr/bin/c++` for the C++ compiler (the GNU C++ compiler) and `/usr/bin/cc` for the C compiler. I don't think the GNU C++ compiler is used to build LLVM, but I'm not sure how to determine which compiler was used when getting LLVM from a package manager and the GNU C/C++ compilers are used in Zig's CI script for Linux.

## Build Zig

Now we should be ready to build Zig. Go to your directory with Zig and follow the [instructions from the Zig README](https://github.com/ziglang/zig#posix-1):
```
mkdir build
cd build
cmake ..
make install
```

The build (depending on your machine) should take a little while. On my old machine with a fresh install of Ubuntu took about 15-20 minutes.

## Extras
### Alias Your Zig Build
Open up your bash profile in a text editor. For Ubuntu if you haven't created any additional profile files, there should be:
* `~/.profile`
* `~/.bashrc`

I used `~/.bashrc` and added the following in it at the bottom of the file:

`alias zig='/home/me/Documents/dev/ziglang/zig/build/zig'`

Then in my Terminal session I did: `$ source ~/.bashrc`

After that try running: `$ zig zen`, which should give you this output:
```

 * Communicate intent precisely.
 * Edge cases matter.
 * Favor reading code over writing code.
 * Only one obvious way to do things.
 * Runtime crashes are better than bugs.
 * Compile errors are better than runtime crashes.
 * Incremental improvements.
 * Avoid local maximums.
 * Reduce the amount one must remember.
 * Minimize energy spent on coding style.
 * Together we serve end users.

```

If it does then it's working as expected and you don't need to specify your full file path to the `zig` executable anymore to build your files.

### Hello Zig!
Try running the sample hello world program that Zig comes with as an example. See the file here: https://github.com/ziglang/zig/blob/master/example/hello_world/hello.zig

```zig
const std = @import("std");

pub fn main() !void {
    // If this program is run without stdout attached, exit with an error.
    const stdout_file = try std.io.getStdOut();
    // If this program encounters pipe failure when printing to stdout, exit
    // with an error.
    try stdout_file.write("Hello, world!\n");
}
```

You can do so by creating a new file with that Zig code and naming it something like `hello_zig.zig`. Go to the file in your Terminal session and run it with:
`$ zig run hello_zig.zig`

If everything is working you should get this output at the Terminal window:
`Hello, world!`


