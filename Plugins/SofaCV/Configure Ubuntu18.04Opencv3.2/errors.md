
-------
Error : opencv nvcc fatal   : Unsupported gpu architecture 'compute_20'
CUDA_GENERATION=Kepler or Maxwell or Pascal

---------
Error : 
[ 10%] Built target IlmImf
In file included from /usr/include/host_config.h:50:0,
                 from /usr/include/cuda_runtime.h:78,
                 from <command-line>:0:
/usr/include/crt/host_config.h:121:2: error: #error -- unsupported GNU version! gcc versions later than 6 are not supported!
 #error -- unsupported GNU version! gcc versions later than 6 are not supported!
  ^~~~~
CUDA不支持6以上的GCC版本。
  
  查了一下看到GCC用的是7.0版本，因为SOFA需要。
  nvcc命令显示的CUDA版本是9.1，而nvidia-smi显示的版本是11.2
  
  卸载旧版本，安装新版本
  https://zhuanlan.zhihu.com/p/72300035
  https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html#overview-drive-os-linux-cudnn-cross-compile
  
  
  CMake Error at cuda_compile_generated_gpu_mat.cu.o.cmake: (message): Error generating opencv-3.2.0/build/modules/core/CMakeFiles/cuda_compile.dir/src/cuda/./cuda_compile_generated_gpu_mat.cu.o
  
  cmake-gui中修改cuda_arch_bin为3.0 3.5 3.7 5.0 5.2 6.0 6.1
  
  
