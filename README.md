# ExpressWeb
A hand-picked visual toolkit for C++. This criteria for the selection was compatibility with MSVC, mingw, GCC, and clang and free use for commercial applications. This was meant to be a low-level selection for a openGL context, mesh loading, texture loading, sound playback, and 
asset archiving. This could possibly provide the basis for a 2D or 3D game engine.

| Library				| Purpose									|
| :---					| :---										|
| Boost::Filesystem		| Filesystem								|
| assimp				| 3d model import							|
| irrXML				| XML parser (for assimp)					|
| zlib					| compression (for assimp)					|
| SOIL					| OpenGL Image Library						|
| ReactPhysics3D		| physics engine library					|
| GLM					| OpenGL Mathematics						|
| glfw					| windows, openGL context, input, events	|
| gl3w					| OpenGL profile loading					|
| ocornut/imgui			| Graphical User interface					|
| libarchive			| Archiving									|
| xiph/vorbis			| Sound										|

Unfortunately, the FBX Software Developer Kit is not available for mingw, and has not been included. Assimp is its inferior replacement.

# Table of Contents

<!--ts-->
   * [Build Instructions](#build-instructions)
   * [Supported](#supported)
   * [Usage Instructions](#usage-instructions)
      * [Creating a server](#creating-a-server)
         * [node.js + hapi.js](#nodejs--hapijs)
         * [C++](#c)
      * [HTTP Routing](#http-routing)
         * [node.js + hapi.js](#nodejs--hapijs-1)
         * [C++](#c-1)
   * [Dependency](#dependency)
   * [License](#license)
<!--te-->

# Build Instructions

No searching around and configuring all the dependencies. All sources are in ```contrib``` and certain libraries are already pre-compiled in ```lib```. After installing boost and configuring paths in the makefile.mk, simply run the make command.

## Prerequisites
Install boost libraries<BR>
The current make script was created for a versioned layout installation of boost:
```shell
./b2 toolset=gcc stage --build-type=complete --layout=versioned
```
Go into the makefile.mk and change the ```BOOST_LIBS_POST``` directive to the correct version. <BR> 
The version for GCC 4.9 on a 32-bit machine for boost 1.67 would look like this:
```make
BOOST_LIBS_POST = -gcc49-mt-x32-1_67
```
The version for MinGW-w64 7.3 on a 64-bit machine for boost 1.67 would look like this:
```make
BOOST_LIBS_POST = -mgw73-mt-x64-1_67
```
If you built boost with ```--layout=system``` then keep the version blank:
```make
BOOST_LIBS_POST = 
```

## Windows
Only Windows host<BR>
Install MinGW-w64<BR>
Add ```<MinGW_PATH>\bin``` to PATH<BR>

## Mac OSX
Only Mac OSX host<BR>
Install clang

## Linux Ubuntu
Install GCC
For C language:
```shell
sudo apt-get install gcc-multilib
```
For C++ language:
```shell
sudo apt-get install g++-multilib
```

## Android
Using Windows x86_64 host for this example:<BR>
Install the android NDK<BR>
Add ```<NDK_PATH>\toolchains\arm-linux-androideabi-4.9\prebuilt\windows-x86_64\bin``` to PATH<BR>
Add ```<NDK_PATH>\toolchains\aarch64-linux-android-4.9\prebuilt\windows-x86_64\bin``` to PATH<BR>
You can manually edit the makefile or make with android ndk path like so:
```sh
make HOST_OS=WIN TARGET_OS=android TARGET_ARCH=x86_64 NDK_PATH=C:\android-ndk-r16b-windows-x86_64\android-ndk-r16b
```

## IOS
Only Mac OSX host<BR>
Install clang<BR>
You can manually edit the makefile or make with iphone sdk path like so:
```sh
make HOST_OS=OSX TARGET_OS=IOS TARGET_ARCH=x86_64 IPHONE_SDK_PATH=/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk
```

## Supported
| Host OS | Target OS | Architecture | Support |  Command |
| :---    | :---      | :---         | :---    | :---     |
| Windows | Windows   | x86          | X    | ``` make HOST_OS=WIN TARGET_ARCH=x86 ``` |
| Windows | Windows   | x86_64       | X    | ``` make HOST_OS=WIN TARGET_ARCH=x86_64 ``` |
| Windows | Android   | armeabi      | O    | ``` make HOST_OS=WIN TARGET_OS=android TARGET_ARCH=armeabi ``` |
| Windows | Android   | armeabi-v7a  | O    | ``` make HOST_OS=WIN TARGET_OS=android TARGET_ARCH=armeabi-v7a ``` |
| Windows | Android   | arm64-v8a    | O    | ``` make HOST_OS=WIN TARGET_OS=android TARGET_ARCH=arm64-v8a ``` |
| Windows | Android   | x86          | O    | ``` make HOST_OS=WIN TARGET_OS=android TARGET_ARCH=x86 ``` |
| Windows | Android   | x86_64       | O    | ``` make HOST_OS=WIN TARGET_OS=android TARGET_ARCH=x86_64 ``` |
| macOS   | macOS     | x86          | O    | ``` make HOST_OS=OSX TARGET_ARCH=x86 ``` |
| macOS   | macOS     | x86_64       | X    | ``` make HOST_OS=OSX TARGET_ARCH=x86_64 ``` |
| macOS   | iOS       | armv7        | O    | ``` make HOST_OS=OSX TARGET_OS=IOS TARGET_ARCH=armv7 ``` |
| macOS   | iOS       | armv7s       | O    | ``` make HOST_OS=OSX TARGET_OS=IOS TARGET_ARCH=armv7s ``` |
| macOS   | iOS       | arm64        | O    | ``` make HOST_OS=OSX TARGET_OS=IOS TARGET_ARCH=arm64 ``` |
| macOS   | iOS       | x86_64       | O    | ``` make HOST_OS=OSX TARGET_OS=IOS TARGET_ARCH=x86_64 ``` |
| linux ubuntu | linux ubuntu | x86     | O    | ``` make HOST_OS=LINUX TARGET_ARCH=x86 ``` |
| linux ubuntu | linux ubuntu | x86_64  | X    | ``` make HOST_OS=LINUX TARGET_ARCH=x86_64 ``` |
 
# Usage Instructions
## Creating a server

### node.js + hapi.js
```node
const Hapi = require('hapi');
const server = new Hapi.Server();
server.connection({ port: 3000 });
server.start((err) => {
    if (err) {
        throw err;
    }
    console.log(`Server running at: ${server.info.uri}`);
});
```

### c++
```cpp
#include <ExpressWeb.h>

auto server = ExpressWeb::Server;
server.connection({ "port", 3000 });
server.start((err) => {
    if (err) {
        throw err;
    }
    console.log(`Server running at: ${server.info.uri}`);
});
```

## HTTP Routing

### node.js + hapi.js
```node
const Hapi = require('hapi');

const server = Hapi.server({
    port: 3000,
    host: 'localhost'
});

server.route({
    method: 'GET',
    path: '/',
    handler: (request, h) => {

        return 'Hello, world!';
    }
});

server.route({
    method: 'GET',
    path: '/{name}',
    handler: (request, h) => {

        return 'Hello, ' + encodeURIComponent(request.params.name) + '!';
    }
});

const init = async () => {

    await server.start();
    console.log(`Server running at: ${server.info.uri}`);
};

process.on('unhandledRejection', (err) => {

    console.log(err);
    process.exit(1);
});

init();
```

### c++
```cpp
#include <ExpressWeb.h>

auto server = ExpressWeb::Server({{ "port", 3000 }, { "host", "localhost" }});
server.route({
    {method: 'GET'},
    {path: '/'},
    {handler: [](ExpressWeb::Request& request, ExpressWeb::Response& response) => {
        return "Hello, world!";
    }
});

server.start((err) => {
    if (err) {
        throw err;
    }
    console.log(`Server running at: ${server.info.uri}`);
});
```


## Dependency

| Library				| License						| Link														|
| :---					| :---							| :---														|
| Boost::Filesystem		| Boost Software License 1.0	| 															|
| assimp				| BSD							| https://github.com/assimp/assimp							|
| irrXML				| zlib							| https://www.ambiera.com/irrxml/							|
| zlib					| zlib							| https://zlib.net/											|
| SOIL					| Public Domain 				| https://www.lonesock.net/soil.html						|
| ReactPhysics3D		| zlib							| https://github.com/DanielChappuis/reactphysics3d			|
| GLM					| MIT							| https://github.com/g-truc/glm								|
| glfw					| zlib							| https://github.com/glfw/glfw								|
| gl3w					| public domain					| https://github.com/skaslev/gl3w							|
| ocornut/imgui			| MIT							| https://github.com/ocornut/imgui							|
| libarchive			| Apache License, Version 2.0	| https://github.com/libarchive/libarchive					|
| xiph/vorbis			| BSD-STYLE						| https://xiph.org/vorbis/									|

# License
The library is licensed under the MIT License: <BR>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.