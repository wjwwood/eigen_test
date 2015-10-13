This repository is designed to test a preceived bug in the CMake infrastructure provided by Eigen on OS X, see: https://github.com/Homebrew/homebrew/issues/35225#issuecomment-147576753

To reproduce:

```
$ brew install eigen  # If not already installed (I have tried 3.2.6 and 3.2.5)
$ cd /tmp
$ git clone https://github.com/wjwwood/eigen_test.git
$ cd eigen_test
$ mkdir build
$ cd build
```

Firs the failure:

```
$ cmake ..
-- The C compiler identification is AppleClang 7.0.0.7000072
-- The CXX compiler identification is AppleClang 7.0.0.7000072
-- Check for working C compiler: /usr/local/opt/ccache/libexec/cc
-- Check for working C compiler: /usr/local/opt/ccache/libexec/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/local/opt/ccache/libexec/c++
-- Check for working CXX compiler: /usr/local/opt/ccache/libexec/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
CMake Error at CMakeLists.txt:4 (find_package):
  By not providing "FindEigen3.cmake" in CMAKE_MODULE_PATH this project has
  asked CMake to find a package configuration file provided by "Eigen3", but
  CMake did not find one.

  Could not find a package configuration file provided by "Eigen3" with any
  of the following names:

    Eigen3Config.cmake
    eigen3-config.cmake

  Add the installation prefix of "Eigen3" to CMAKE_PREFIX_PATH or set
  "Eigen3_DIR" to a directory containing one of the above files.  If "Eigen3"
  provides a separate development package or SDK, be sure it has been
  installed.


-- Configuring incomplete, errors occurred!
See also "/tmp/eigen_test/build/CMakeFiles/CMakeOutput.log".
```

This can be "fixed":

```
$ rm -rf ./*  # In /tmp/eigen_test/build
$ cmake .. -DCMAKE_MODULE_PATH=/usr/local/share/cmake/Modules
CMake Warning (dev) at /usr/local/Cellar/cmake/3.3.2/share/cmake/Modules/CMakeDetermineCompilerId.cmake:100 (include):
  File
  /usr/local/Cellar/cmake/3.3.2/share/cmake/Modules/CMakeDetermineCompilerId.cmake
  includes /usr/local/share/cmake/Modules/CMakeCompilerIdDetection.cmake
  (found via CMAKE_MODULE_PATH) which shadows
  /usr/local/Cellar/cmake/3.3.2/share/cmake/Modules/CMakeCompilerIdDetection.cmake.
  This may cause errors later on .

  Policy CMP0017 is not set: Prefer files from the CMake module directory
  when including from there.  Run "cmake --help-policy CMP0017" for policy
  details.  Use the cmake_policy command to set the policy and suppress this
  warning.
Call Stack (most recent call first):
  /usr/local/share/cmake/Modules/CMakeDetermineCCompiler.cmake:108 (include)
  CMakeLists.txt:2 (project)
This warning is for project developers.  Use -Wno-dev to suppress it.

-- The C compiler identification is AppleClang 7.0.0.7000072
CMake Warning (dev) at /usr/local/Cellar/cmake/3.3.2/share/cmake/Modules/CMakeDetermineCompilerId.cmake:100 (include):
  File
  /usr/local/Cellar/cmake/3.3.2/share/cmake/Modules/CMakeDetermineCompilerId.cmake
  includes /usr/local/share/cmake/Modules/CMakeCompilerIdDetection.cmake
  (found via CMAKE_MODULE_PATH) which shadows
  /usr/local/Cellar/cmake/3.3.2/share/cmake/Modules/CMakeCompilerIdDetection.cmake.
  This may cause errors later on .

  Policy CMP0017 is not set: Prefer files from the CMake module directory
  when including from there.  Run "cmake --help-policy CMP0017" for policy
  details.  Use the cmake_policy command to set the policy and suppress this
  warning.
Call Stack (most recent call first):
  /usr/local/share/cmake/Modules/CMakeDetermineCXXCompiler.cmake:103 (include)
  CMakeLists.txt:2 (project)
This warning is for project developers.  Use -Wno-dev to suppress it.

-- The CXX compiler identification is AppleClang 7.0.0.7000072
-- Check for working C compiler: /usr/local/opt/ccache/libexec/cc
-- Check for working C compiler: /usr/local/opt/ccache/libexec/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/local/opt/ccache/libexec/c++
-- Check for working CXX compiler: /usr/local/opt/ccache/libexec/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found Eigen3: /usr/local/include/eigen3 (Required is at least version "2.91.0")
-- Configuring done
-- Generating done
-- Build files have been written to: /tmp/eigen_test/build
```

I think the warning is due to the fact that I set the CMAKE_MODULE_PATH to the same place as the default (but through a symlink).
