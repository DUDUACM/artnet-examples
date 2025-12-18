# CMake Build Instructions

This project supports building with CMake as a modern alternative to the traditional Autotools build system.

## Prerequisites

- CMake >= 3.15
- C compiler (GCC, Clang, MSVC, or MinGW)
- **libartnet >= 1.1.0** - ArtNet protocol library
- Git (for cloning libartnet if building from source)
- (Optional) ncurses library - for building artnet_dmxconsole and artnet_dmxmonitor

## Platform-Specific Requirements

### Linux
- Build tools: `build-essential`, `cmake`
- libartnet development package or source
- (Optional) `libncurses-dev` for console programs

### macOS
- Xcode Command Line Tools or GCC
- CMake (via Homebrew: `brew install cmake`)
- libartnet (via Homebrew or build from source)
- (Optional) ncurses (usually pre-installed)

### Windows
**MSYS2 Environment (Recommended)**
- MSYS2 from https://www.msys2.org/
- MinGW64 toolchain and CMake
- libartnet built in MSYS2 environment

## Installing libartnet

### Option 1: Use FetchContent (Automatic Download)

By default, CMake will automatically download and build libartnet from GitHub if it's not found on your system. This requires:
- Git installed and accessible
- Internet connection
- On Windows: MSYS2/MinGW environment with autotools

### Option 2: Install from Package Manager

**Ubuntu/Debian:**
```bash
sudo apt-get install libartnet-dev
```

**Fedora/RHEL:**
```bash
sudo dnf install libartnet-devel
```

**macOS (Homebrew):**
```bash
brew install libartnet
```

### Option 3: Build from Source

**Linux/macOS:**
```bash
git clone https://github.com/OpenLightingProject/libartnet.git
cd libartnet
./autogen.sh
./configure --prefix=/usr/local
make
sudo make install
```

**Windows (MSYS2 MINGW64):**
```bash
git clone https://github.com/OpenLightingProject/libartnet.git
cd libartnet
./autogen.sh
./configure --prefix=/mingw64
make
make install
```

## Basic Build Steps

### Linux/macOS

```bash
# Navigate to project directory
cd artnet-examples

# Create build directory
mkdir build
cd build

# Configure project
cmake ..

# Build
cmake --build .

# Install (optional)
sudo cmake --install .
```

### Windows (MSYS2 MINGW64)

```bash
# Open MSYS2 MINGW64 terminal
cd /d/work_space/opensource_project/artnet-examples

# Create build directory
mkdir build
cd build

# Configure with Ninja generator
cmake .. -G "Ninja"

# Build
ninja

# Or use standard make-based build
cmake --build .
```

### Windows (Visual Studio)

```powershell
# Open PowerShell or CMD
cd D:\work_space\opensource_project\artnet-examples

# Create build directory
mkdir build
cd build

# Configure for Visual Studio
cmake .. -G "Visual Studio 17 2022" -A x64

# Build
cmake --build . --config Release

# Or open the generated .sln file in Visual Studio
```

## CMake Configuration Options

### BUILD_NCURSES_PROGS

Control whether to build programs that require ncurses (artnet_dmxconsole and artnet_dmxmonitor).

```bash
# Disable ncurses programs
cmake .. -DBUILD_NCURSES_PROGS=OFF
```

Default: `ON` (enabled)

### Specifying libartnet Location

If CMake cannot automatically find libartnet, you can specify its location:

```bash
cmake .. \
  -DLIBARTNET_INCLUDE_DIRS="/path/to/libartnet/include" \
  -DLIBARTNET_LIBRARIES="/path/to/libartnet/lib/libartnet.a"
```

### Build Type

```bash
# Debug build (with debug symbols)
cmake .. -DCMAKE_BUILD_TYPE=Debug

# Release build (optimized)
cmake .. -DCMAKE_BUILD_TYPE=Release

# Release with debug info
cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo
```

### Installation Prefix

```bash
# Install to custom location
cmake .. -DCMAKE_INSTALL_PREFIX=$HOME/.local

# System-wide installation (default: /usr/local)
cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local
```

## Built Programs

After successful compilation, executables will be located in the `build/src/` directory:

### Core Programs (All Platforms)
- `artnet_discover` - Discover ArtNet nodes on the network
- `artnet_setdmx` - Set DMX channel values
- `artnet_flood_rx` - ArtNet flood receiver for testing
- `artnet_flood_tx` - ArtNet flood transmitter for testing
- `artnet_profile_rx` - Performance profiling receiver
- `artnet_profile_tx` - Performance profiling transmitter
- `artnet_firmware_node` - Firmware update node example
- `artnet_firmware_server` - Firmware update server example
- `artnet_multiport` - Multi-port example
- `artnet_rdm_output` - RDM output example

### Console Programs (Requires ncurses)
- `artnet_dmxconsole` - Console-based DMX controller
- `artnet_dmxmonitor` - DMX data monitor

### Platform-Specific Programs
- `artnet_usb` - ArtNet to DMX node (Linux only, requires Enttec Open USB)

## Dependency Management

CMake will attempt to find libartnet using the following methods in order:

1. **pkg-config** (if available) - searches for libartnet >= 1.1.0
2. **FetchContent** - automatically downloads and builds from GitHub if not found
3. **Manual specification** - uses paths provided via CMake variables

### How FetchContent Works

When libartnet is not found on the system:
1. CMake downloads libartnet source from GitHub
2. Configures and builds it using autotools (requires sh, autogen.sh, configure)
3. Links the built library to example programs

**Note:** On Windows, this requires MSYS2/Cygwin environment for autotools support.

## Platform-Specific Notes

### Linux
- Full support for all programs including artnet_usb
- Requires pthread library (automatically linked)
- ncurses programs available if libncurses-dev is installed

### macOS
- Supports all programs except artnet_usb
- ncurses usually pre-installed
- May need to specify library paths if using Homebrew

### Windows
- Supports all programs except artnet_usb
- Requires `ws2_32` and `winmm` libraries (automatically linked by CMake)
- ncurses programs only available in MSYS2 environment
- Recommend using MSYS2 MINGW64 for best compatibility

## Build Examples

### Example 1: Quick Build with Auto-Download

```bash
mkdir build && cd build
cmake ..
cmake --build .
```

CMake will automatically download and build libartnet if not found.

### Example 2: Using System libartnet

```bash
mkdir build && cd build
cmake ..  # Will use pkg-config to find system libartnet
cmake --build .
```

### Example 3: Custom libartnet Location

```bash
mkdir build && cd build
cmake .. \
  -DLIBARTNET_INCLUDE_DIRS="$HOME/libartnet/include" \
  -DLIBARTNET_LIBRARIES="$HOME/libartnet/lib/libartnet.a"
cmake --build .
```

### Example 4: Windows MSYS2 Build

```bash
# In MSYS2 MINGW64 terminal
mkdir build && cd build
cmake .. -G "Ninja" -DCMAKE_BUILD_TYPE=Release
ninja
```

### Example 5: Visual Studio Build

```powershell
mkdir build
cd build
cmake .. -G "Visual Studio 17 2022" -A x64
cmake --build . --config Release
```

### Example 6: Build Without ncurses Programs

```bash
mkdir build && cd build
cmake .. -DBUILD_NCURSES_PROGS=OFF
cmake --build .
```

## Installation

After building, you can install the programs:

```bash
# System-wide installation (requires sudo/admin)
sudo cmake --install .

# User-local installation
cmake --install . --prefix $HOME/.local

# Windows installation
cmake --install . --prefix C:/Program\ Files/artnet-examples
```

## Running Examples

### artnet_discover - Discover ArtNet Nodes

```bash
# Linux/macOS
./build/src/artnet_discover -a 192.168.1.100 -t 3

# Windows
.\build\src\Release\artnet_discover.exe -a 192.168.1.100 -t 3
```

Options:
- `-a <ip>` - IP address to bind to
- `-t <seconds>` - Time to wait for replies (default: 2)

### artnet_setdmx - Set DMX Values

```bash
# Set channel 1 to value 255
./build/src/artnet_setdmx -a 192.168.1.100 -p 0 -c 1 -d 255

# Fade channel 10 to 128 over 2 seconds
./build/src/artnet_setdmx -a 192.168.1.100 -c 10 -d 128 -f 2.0
```

Options:
- `-a <ip>` - IP address to send from
- `-p <port>` - Universe (port) address (0-4)
- `-c <channel>` - DMX channel (1-512)
- `-d <value>` - DMX value (0-255)
- `-f <time>` - Fade time in seconds

### artnet_dmxconsole - Interactive DMX Console

```bash
./build/src/artnet_dmxconsole -a 192.168.1.100 -s 2 -p 3
```

Options:
- `-a <ip>` - IP address
- `-s <subnet>` - Subnet address (0-15)
- `-p <port>` - Port address (0-15)

Universe address is calculated as: `(subnet << 4) | port`

## Troubleshooting

### libartnet Not Found

**Error:** `libartnet not found`

**Solutions:**
1. Install libartnet via package manager
2. Build libartnet from source
3. Let CMake auto-download (ensure Git is installed)
4. Specify paths manually with `-DLIBARTNET_INCLUDE_DIRS` and `-DLIBARTNET_LIBRARIES`

### Windows: Cannot Find libartnet

**Error:** CMake cannot find libartnet on Windows

**Solution:**
Build libartnet in MSYS2 first:
```bash
# In MSYS2 MINGW64 terminal
git clone https://github.com/OpenLightingProject/libartnet.git
cd libartnet
./autogen.sh
./configure --prefix=/mingw64
make && make install
```

### Windows: select() Undefined Reference

**Error:** `undefined reference to '__imp_select'`

**Solution:**
This should be automatically fixed by CMake linking `ws2_32` library. If you still see this error:
- Ensure you're building in MSYS2 MINGW64 environment
- Check that `ws2_32` is being linked (it's automatic in our CMakeLists.txt)

### Windows: winsock2.h Header Order Warning

**Error:** `#warning Please include winsock2.h before windows.h`

**Solution:**
This has been fixed in the source code. If you still see it:
- Make sure you're using the latest version
- The source files now include `winsock2.h` before `windows.h`

### ncurses Programs Not Building

**Error:** ncurses programs are skipped

**Solutions:**
- **Linux/macOS:** Install ncurses development package
  ```bash
  # Ubuntu/Debian
  sudo apt-get install libncurses-dev
  
  # Fedora/RHEL
  sudo dnf install ncurses-devel
  
  # macOS
  brew install ncurses
  ```
- **Windows:** ncurses programs require MSYS2 environment with ncurses package
- **Disable ncurses programs:** Use `-DBUILD_NCURSES_PROGS=OFF`

### FetchContent Download Fails

**Error:** Git clone fails or autotools not found

**Solutions:**
1. Check internet connection
2. Install Git: `sudo apt-get install git` or download from git-scm.com
3. On Windows, ensure MSYS2 has autotools:
   ```bash
   pacman -S autoconf automake libtool
   ```
4. Install libartnet manually and use system version

### Build Fails with -Werror

**Error:** Compilation fails due to warnings treated as errors

**Solution:**
Warnings are treated as errors by default. If you encounter platform-specific warnings:
1. Report the warning as a bug
2. Temporary workaround: Edit CMakeLists.txt to remove `-Werror` flag

## Advanced Configuration

### Cross-Compilation

For cross-compiling to different architectures:

```bash
cmake .. \
  -DCMAKE_TOOLCHAIN_FILE=/path/to/toolchain.cmake \
  -DCMAKE_INSTALL_PREFIX=/path/to/install
```

### Building Static Executables

```bash
cmake .. -DCMAKE_EXE_LINKER_FLAGS="-static"
cmake --build .
```

### Verbose Build Output

```bash
cmake --build . --verbose
# Or with make
make VERBOSE=1
```

### Parallel Building

```bash
# Use all CPU cores
cmake --build . --parallel

# Specify number of jobs
cmake --build . -j 4
```

## Comparison with Autotools

| Feature | CMake | Autotools |
|---------|-------|-----------|
| Configuration Speed | Fast | Slower |
| Windows Support | Native | Requires MSYS2/Cygwin |
| IDE Integration | Excellent | Limited |
| Parallel Builds | Default | Requires -j flag |
| Dependency Finding | Modern (pkg-config + FetchContent) | pkg-config only |
| Cross-Platform | Excellent | Good (Unix-focused) |

## Contributing

When contributing to the CMake build system:

1. Test on multiple platforms (Linux, macOS, Windows)
2. Ensure both system libartnet and FetchContent methods work
3. Maintain compatibility with CMake 3.15+
4. Update this documentation for any new options or changes

## Additional Resources

- **CMake Documentation:** https://cmake.org/documentation/
- **libartnet Repository:** https://github.com/OpenLightingProject/libartnet
- **ArtNet Protocol:** https://art-net.org.uk/
- **MSYS2 Setup:** https://www.msys2.org/

## License

This project follows the same license as the original artnet-examples project (see COPYING file).

The CMake build system additions are also distributed under the same terms.
