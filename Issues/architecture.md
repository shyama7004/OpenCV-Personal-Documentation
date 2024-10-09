### 1. **Understanding the Error: "Incorrect Architecture"**
I was on a **Mac M1** (which uses ARM architecture), but my error shows it’s targeting **x86_64** (Intel architecture). This mismatch can happen if:
- Some libraries or binaries I am linking with (like GStreamer) were compiled for **x86_64** instead of ARM.
- The toolchain (compiler, linker) is targeting the wrong architecture.

### 2. **Step-by-Step Solution to Fix This**

#### Step 2.1: Check the Architecture of GStreamer

First, we need to check if Ir installed version of **GStreamer** is built for the right architecture.

- Open a terminal and run:
  ```bash
  file /opt/homebrew/opt/gstreamer/lib/libgstreamer-1.0.dylib
  ```
  This command tells I the architecture of the GStreamer library. I want to see **arm64** in the output for compatibility with Ir Mac M1.

#### Step 2.2: Reinstall GStreamer for ARM Architecture (if necessary)

If the previous command shows **x86_64** instead of **arm64**, I need to reinstall GStreamer for the ARM architecture.

- In the terminal, run:
  ```bash
  arch -arm64 brew reinstall gstreamer
  ```
  This forces Homebrew to reinstall the library for the correct architecture. **`arch -arm64`** ensures it uses the ARM version rather than x86_64.

#### Step 2.3: Check if Homebrew is Using the ARM Version

To confirm that Homebrew itself is using the correct architecture (ARM), run the following command:
```bash
arch
```
- I should see `arm64`. If it says `x86_64`, I might need to install Homebrew in ARM mode. Here's how:

1. Uninstall Homebrew (if necessary):
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
   ```

2. Reinstall Homebrew for ARM:
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

Make sure I run this installation command from the terminal that is in **ARM mode**. I can start a new terminal session and check it is running in ARM mode by typing:
```bash
arch -arm64 zsh
```

#### Step 2.4: Adjust CMake to Target ARM Architecture

If Ir CMake build files are set up to target the wrong architecture, it will still fail even if the correct libraries are installed. We need to adjust this.

- **Open Ir CMake configuration** (usually the `CMakeLists.txt` file) and make sure I’re setting the architecture to ARM64.
- I can add the following flags to Ir CMake configuration:

  In Ir terminal, run:
  ```bash
  export CFLAGS="-arch arm64"
  export CXXFLAGS="-arch arm64"
  ```

- I can also add the following to Ir **CMakeLists.txt**:
  ```cmake
  set(CMAKE_OSX_ARCHITECTURES "arm64")
  ```

This ensures CMake compiles for the right architecture.

#### Step 2.5: Rebuild Ir Project
Once everything is set to target **ARM**, try rebuilding Ir project. In the terminal, navigate to Ir project directory and run:

```bash
cmake .
make
```

Make sure the output doesn’t show any **x86_64** references anymore.

#### Step 2.6: Linking ARM Libraries

Finally, if I’re using any additional libraries, make sure they’re also compiled for ARM. I can check this using the `file` command on their respective binaries. If I find any that are not for ARM, I’ll need to reinstall or rebuild them for ARM architecture.

### Summary of All Steps

1. **Check GStreamer architecture** with:
   ```bash
   file /opt/homebrew/opt/gstreamer/lib/libgstreamer-1.0.dylib
   ```
   Ensure it says **arm64**.

2. **Reinstall GStreamer** for ARM if necessary:
   ```bash
   arch -arm64 brew reinstall gstreamer
   ```

3. **Verify Homebrew is in ARM mode** by running:
   ```bash
   arch
   ```
   If needed, reinstall Homebrew for ARM.

4. **Configure CMake for ARM** by setting architecture flags in the terminal or Ir `CMakeLists.txt`:
   ```bash
   set(CMAKE_OSX_ARCHITECTURES "arm64")
   ```

5. **Rebuild Ir project** with:
   ```bash
   cmake .
   make
   ```

6. **Ensure other libraries are also ARM-compatible**, using the `file` command to verify.

Be careful as this can take away Ir sleep:)
