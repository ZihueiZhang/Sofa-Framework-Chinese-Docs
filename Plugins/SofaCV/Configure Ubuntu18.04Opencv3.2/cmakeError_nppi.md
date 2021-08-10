参考：https://stackoverflow.com/questions/46584000/cmake-error-variables-are-set-to-notfound

**ERROR**: CMake Error: The following variables are used in this project, but they are set to NOTFOUND.
Please set them or make sure they are set and tested correctly in the CMake files:
CUDA_nppi_LIBRARY (ADVANCED)


I tried the following and it worked:

Change in FindCUDA.cmake the nppi library to the several splitted ones. This has to be done in 3 places. Remember this change is just to make it work with CUDA 9.0, I am not doing checks for version or anything, which should be done if you plan to give it to different people with different CUDA versions.

1) look for the line with:

find_cuda_helper_libs(nppi)
and replace it with the lines:

  find_cuda_helper_libs(nppial)
  find_cuda_helper_libs(nppicc)
  find_cuda_helper_libs(nppicom)
  find_cuda_helper_libs(nppidei)
  find_cuda_helper_libs(nppif)
  find_cuda_helper_libs(nppig)
  find_cuda_helper_libs(nppim)
  find_cuda_helper_libs(nppist)
  find_cuda_helper_libs(nppisu)
  find_cuda_helper_libs(nppitc)
2) find the line:

set(CUDA_npp_LIBRARY "${CUDA_nppc_LIBRARY};${CUDA_nppi_LIBRARY};${CUDA_npps_LIBRARY}")
and change it to

set(CUDA_npp_LIBRARY "${CUDA_nppc_LIBRARY};${CUDA_nppial_LIBRARY};${CUDA_nppicc_LIBRARY};${CUDA_nppicom_LIBRARY};${CUDA_nppidei_LIBRARY};${CUDA_nppif_LIBRARY};${CUDA_nppig_LIBRARY};${CUDA_nppim_LIBRARY};${CUDA_nppist_LIBRARY};${CUDA_nppisu_LIBRARY};${CUDA_nppitc_LIBRARY};${CUDA_npps_LIBRARY}")
3) find the unset variables and add the new variables as well So, find:

unset(CUDA_nppi_LIBRARY CACHE)
and change it to:

unset(CUDA_nppial_LIBRARY CACHE)
unset(CUDA_nppicc_LIBRARY CACHE)
unset(CUDA_nppicom_LIBRARY CACHE)
unset(CUDA_nppidei_LIBRARY CACHE)
unset(CUDA_nppif_LIBRARY CACHE)
unset(CUDA_nppig_LIBRARY CACHE)
unset(CUDA_nppim_LIBRARY CACHE)
unset(CUDA_nppist_LIBRARY CACHE)
unset(CUDA_nppisu_LIBRARY CACHE)
unset(CUDA_nppitc_LIBRARY CACHE)
And also in OpenCVDetectCUDA.cmake you have to remove the 2.0 architechture which is no longer supported.

It has:

  ...
  set(__cuda_arch_ptx "")
  if(CUDA_GENERATION STREQUAL "Fermi")
    set(__cuda_arch_bin "2.0")
  elseif(CUDA_GENERATION STREQUAL "Kepler")
    set(__cuda_arch_bin "3.0 3.5 3.7")
  ...
It should be:

  ...
  set(__cuda_arch_ptx "")
  if(CUDA_GENERATION STREQUAL "Kepler")
    set(__cuda_arch_bin "3.0 3.5 3.7")
  elseif(CUDA_GENERATION STREQUAL "Maxwell")
    set(__cuda_arch_bin "5.0 5.2")
  ...
Basically I removed the first if and the first elif turns to an if.

As mentionned by @matko It also has :

set(__cuda_arch_bin "2.0 3.0 3.5 3.7 5.0 5.2 6.0 6.1") 
Which should be changed to:

set(__cuda_arch_bin "3.0 3.5 3.7 5.0 5.2 6.0 6.1") 
One last thing it is needed. CUDA 9.0 has a separated file for the halffloat (cuda_fp16.h) now. This needs to be included in OpenCV.

From CUDA 9.0 manual:

Unsupported Features General CUDA ‣ CUDA library. The built-in functions __float2half_rn() and __half2float() have been removed. Use equivalent functionality in the updated fp16 header file from the CUDA toolkit.

To do this, you need to add:

#include <cuda_fp16.h>
in the header file

opencv-3.3.0\modules\cudev\include\opencv2\cudev\common.hpp
This are the basics for a definite patch for OpenCV. What it is missing, well as I told you before, I do not care about CUDA versions (it needs an IF). Also, CUDA 9.0 has a bunch of deprecated functions used by OpenCV ... this probably will be replaced by the OpenCV team at some point. It is also possible that one or more of the splitted libraries of nppi is not used.

Final recommendations: For this kind of complex cmakes with so many options you should use ccmake (sudo apt-get install cmake-curses-gui) to be able to change easily the variables or at least view the values, or a real GUI one.

For other people with windows and visual studio 7, I also had to change the CUDA_HOST_COMPILER variable, else you get a bunch of errors with cmd.exe exit with code 1 or something similar... it seems it couldn't get there with the autodetected one.

This worked for me with OpenCV 3.3 and CUDA 9.0 and Visual Studio 2017 with Windows 10. I think it should work also in Ubuntu, since the error and the changes are related to CUDA. I haven't tested it much, I compiled and run the some of the performance tests and all of them passed... So I think everything worked ok.
