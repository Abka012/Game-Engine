# CLUA: C++ Core Library with Application Framework

CLUA is a C++ application framework that provides a foundational library for building cross-platform applications with OpenGL rendering and an immediate-mode GUI. This project includes a shared core library, an editor application, and a runtime application, all built using modern CMake.

## Features

- **Cross-Platform Support**: Built to run on Windows, macOS, and Linux
- **OpenGL 3.3 Core Profile**: Modern OpenGL rendering pipeline
- **ImGUI Integration**: Full-featured immediate-mode GUI with docking and viewport support
- **CMake Build System**: Streamlined configuration and building for multiple platforms
- **Shared Core Library**: Modular design with a shared library containing core functionality
- **Font Support**: Custom font loading with comprehensive Unicode character set

## Project Structure

```
Game-Engine/
├── CMakeLists.txt          # Main build configuration
├── CORE/                   # Core library source code
│   └── src/
│       ├── core.h          # Core library header
│       └── core.cpp        # Core library implementation
├── EDITOR/                 # Editor application
│   └── src/
│       └── main.cpp        # Editor entry point
├── RUNTIME/                # Runtime application
│   └── src/
│       └── main.cpp        # Runtime entry point
├── resources/              # Application resources (fonts, etc.)
├── submodules/             # Git submodules (GLFW, ImGUI)
└── app.rc                  # Windows resource file
```

## Prerequisites

- CMake 3.5.0 or higher
- A C++ compiler with C++11 support (GCC 4.8+, Clang 3.3+, MSVC 2015+)
- OpenGL 3.3 or higher compatible graphics driver

## Building the Project

### Clone the Repository (with submodules)

```bash
git clone --recursive <repository-url>
cd Game-Engine
```

If you already cloned without submodules, initialize them:

```bash
cd Game-Engine
git submodule update --init --recursive
```

### Build on Linux/macOS

```bash
mkdir build
cd build
cmake ..
make
```

### Build on Windows (using Visual Studio)

```bash
mkdir build
cd build
cmake .. -G "Visual Studio 17 2022"
```

Then open the generated solution file in Visual Studio.

## Running the Applications

After building, the executables will be located in the `build/bin/` directory:

### Running the Editor

```bash
cd build/bin
./Editor
```

Or on Windows:
```cmd
cd build\bin
Editor.exe
```

### Running the Runtime Application

```bash
cd build/bin
./Runtime
```

Or on Windows:
```cmd
cd build\bin
Runtime.exe
```

## Notes

- Both the Editor and Runtime applications use the same `Exec()` function from the Core library, which initializes the window, OpenGL context, and ImGUI.
- The applications expect a `resources/` directory with the font file `Aller_Lt.ttf` to be present in the working directory.
- On Linux, there's a known issue with mouse position in ImGui viewports, which is handled by enabling window decorations.
