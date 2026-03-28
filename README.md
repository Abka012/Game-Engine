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


---

## 🛠️ Common Build Issues & Debugging Guide

### 🔴 CMake Errors: Missing Submodules or CMakeLists.txt

**Error Example:**
```
CMake Error at submodules/glfw/src/CMakeLists.txt:181 (message):
  RandR headers not found; install libxrandr development package
```

**Solutions:**

1. **Verify submodules are initialized:**
   ```bash
   git submodule status
   ```
   If you see `-` (uninitialized), run:
   ```bash
   git submodule update --init --recursive
   ```

2. **If submodules are broken or empty:**
   ```bash
   # Remove existing submodules (this won't delete your code)
   git submodule deinit -f .
   git submodule update --init --recursive
   ```

3. **Manually clone submodules if needed:**
   ```bash
   mkdir -p submodules
   git clone https://github.com/glfw/glfw.git submodules/glfw
   git clone https://github.com/ocornut/imgui.git submodules/imgui
   ```

---

### 🔴 Missing OpenGL/X11 Dependencies (Linux)

**Error Examples:**
```
Could NOT find OpenGL (missing: OPENGL_opengl_LIBRARY OPENGL_glx_LIBRARY)
RandR headers not found; install libxrandr development package
```

**Fix (Debian/Ubuntu):**
```bash
sudo apt-get update
sudo apt-get install \
    libgl1-mesa-dev \
    libglx-dev \
    libx11-dev \
    libxrandr-dev \
    libxinerama-dev \
    libxcursor-dev \
    libxi-dev \
    libxext-dev \
    libxrender-dev \
    libxkbcommon-dev
```

**Fix (Fedora/RHEL/CentOS):**
```bash
sudo dnf install \
    mesa-libGL-devel \
    libglvnd-devel \
    libX11-devel \
    libXrandr-devel \
    libXinerama-devel \
    libXcursor-devel \
    libXi-devel \
    libXext-devel \
    libXrender-devel \
    libxkbcommon-devel
```

**Fix (Arch Linux):**
```bash
sudo pacman -S \
    libgl \
    libglx \
    libx11 \
    libxrandr \
    libxinerama \
    libxcursor \
    libxi \
    libxext \
    libxrender \
    libxkbcommon
```

After installing dependencies, **clean and rebuild**:
```bash
rm -rf build
mkdir build && cd build
cmake ..
make
```

---

### 🔴 ImGui Compatibility Errors

**Error Examples:**
```
error: ‘ImGuiConfigFlags_ViewportsEnable’ was not declared in this scope
error: ‘DockSpace’ is not a member of ‘ImGui’
error: ‘SetNextWindowViewport’ is not a member of ‘ImGui’
```

**Cause:** You're using an older version of ImGui that doesn't support the docking/viewport API.

**Fix:**

1. **Check your ImGui version:**
   ```bash
   grep "IMGUI_VERSION" submodules/imgui/imgui.h
   ```

2. **Update to the latest version:**
   ```bash
   cd submodules/imgui
   git pull origin main
   # OR for the docking branch (required for DockSpace/Viewports):
   git checkout docking
   git pull origin docking
   ```

3. **Ensure all ImGui files match version:**
   Make sure `imgui.h`, `imgui.cpp`, `imgui_impl_glfw.cpp`, and `imgui_impl_opengl3.cpp` are all from the same version.

---

### 🔴 Font Loading Errors

**Error Example:**
```
Failed to load font: resources/Aller_Lt.ttf
```

**Fix:**
1. Ensure the `resources/` directory exists:
   ```bash
   ls -la resources/
   ```

2. If `Aller_Lt.ttf` is missing, either:
   - Place the font file in `resources/Aller_Lt.ttf`, OR
   - Modify the font loading code in `CORE/src/core.cpp` to use a different font path.

**Tip:** For testing, you can use a system font temporarily:
```cpp
// Example: Load a system font (Linux)
io.Fonts->AddFontFromFileTTF("/usr/share/fonts/truetype/dejavu/DejaVuSans.ttf", 16.0f);
```

---

### 🔴 CMake Configuration Issues

**Problem:** CMake cache conflicts after changing dependencies

**Solution:**
```bash
# Clean CMake cache
rm -rf CMakeCache.txt CMakeFiles/
# Or if in build directory:
rm -rf build/
mkdir build && cd build
cmake ..
```

---

### 🔴 Windows-Specific Issues

**Missing DLLs at runtime:**
- Ensure `opengl32.lib` is linked (CMake handles this automatically)
- If using dynamic OpenGL libraries, ensure `opengl32.dll` is available (standard on Windows)

**Path issues with resources:**
- Run executables from the project root, OR
- Set working directory to project root in Visual Studio:
  - Right-click project → Properties → Debugging → Working Directory: `$(ProjectDir)`

---

### 🔧 General Debugging Workflow

1. **Always start fresh when debugging:**
   ```bash
   git submodule update --init --recursive
   rm -rf build
   mkdir build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Debug  # Enable debug symbols
   make VERBOSE=1  # See full compilation commands
   ```

2. **Check Git submodule health:**
   ```bash
   git submodule status
   # All should show nothing or a commit hash, not "-" or "+"
   ```

3. **Verify GLFW initialization:**
   Add temporary logging to `submodules/glfw/src/context.c` if window creation fails.

4. **Enable verbose CMake output:**
   ```bash
   cmake .. --debug-trycompile -DCMAKE_VERBOSE_MAKEFILE=ON
   ```

5. **Check system OpenGL support:**
   ```bash
   # On Linux, check if OpenGL is available
   glxinfo | grep "OpenGL version"
   glxinfo | grep "OpenGL renderer"
   ```

---

## 📝 Additional Notes

- Both the Editor and Runtime applications use the same `Exec()` function from the Core library, which initializes the window, OpenGL context, and ImGUI.
- The applications expect a `resources/` directory with the font file `Aller_Lt.ttf` to be present in the working directory.
- On Linux, there's a known issue with mouse position in ImGui viewports, which is handled by enabling window decorations.
- For development, consider using `cmake --build . --parallel` to speed up builds on multi-core systems.
- When reporting issues, include:
  - Your OS and version
  - CMake version (`cmake --version`)
  - Compiler version (`gcc --version` or `clang --version`)
  - Full error logs
