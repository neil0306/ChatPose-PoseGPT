直接运行`main_chat.py`报错:
```txt
Traceback (most recent call last):
  File "/home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/utils/cpp_extension.py", line 1900, in _run_ninja_build
    subprocess.run(
  File "/home/ning/anaconda3/envs/chatpose/lib/python3.9/subprocess.py", line 528, in run
    raise CalledProcessError(retcode, process.args,
subprocess.CalledProcessError: Command '['ninja', '-v']' returned non-zero exit status 1.

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "/home/ning/Desktop/get_skeleton_test/ChatPose-PoseGPT/main_chat.py", line 11, in <module>
    from model.chatpose import ChatPoseForCausalLM
  File "/home/ning/Desktop/get_skeleton_test/ChatPose-PoseGPT/model/chatpose.py", line 13, in <module>
    set_rasterizer(type = 'standard')
  File "/home/ning/Desktop/get_skeleton_test/ChatPose-PoseGPT/model/smpl/renderer.py", line 30, in set_rasterizer
    load(name='standard_rasterize_cuda', 
  File "/home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/utils/cpp_extension.py", line 1284, in load
    return _jit_compile(
  File "/home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/utils/cpp_extension.py", line 1508, in _jit_compile
    _write_ninja_file_and_build_library(
  File "/home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/utils/cpp_extension.py", line 1623, in _write_ninja_file_and_build_library
    _run_ninja_build(
  File "/home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/utils/cpp_extension.py", line 1916, in _run_ninja_build
    raise RuntimeError(message) from e
RuntimeError: Error building extension 'standard_rasterize_cuda': [1/2] /usr/local/cuda/bin/nvcc  -DTORCH_EXTENSION_NAME=standard_rasterize_cuda -DTORCH_API_INCLUDE_EXTENSION_H -DPYBIND11_COMPILER_TYPE=\"_gcc\" -DPYBIND11_STDLIB=\"_libstdcpp\" -DPYBIND11_BUILD_ABI=\"_cxxabi1011\" -isystem /home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/include -isystem /home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/include/torch/csrc/api/include -isystem /home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/include/TH -isystem /home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/include/THC -isystem /usr/local/cuda/include -isystem /home/ning/anaconda3/envs/chatpose/include/python3.9 -D_GLIBCXX_USE_CXX11_ABI=0 -D__CUDA_NO_HALF_OPERATORS__ -D__CUDA_NO_HALF_CONVERSIONS__ -D__CUDA_NO_BFLOAT16_CONVERSIONS__ -D__CUDA_NO_HALF2_OPERATORS__ --expt-relaxed-constexpr -gencode=arch=compute_75,code=sm_75 -gencode=arch=compute_86,code=compute_86 -gencode=arch=compute_86,code=sm_86 --compiler-options '-fPIC' -std=c++14 -ccbin=$(which gcc-7) -c /home/ning/Desktop/get_skeleton_test/ChatPose-PoseGPT/model/smpl/rasterizer/standard_rasterize_cuda_kernel.cu -o standard_rasterize_cuda_kernel.cuda.o 
FAILED: standard_rasterize_cuda_kernel.cuda.o 
/usr/local/cuda/bin/nvcc  -DTORCH_EXTENSION_NAME=standard_rasterize_cuda -DTORCH_API_INCLUDE_EXTENSION_H -DPYBIND11_COMPILER_TYPE=\"_gcc\" -DPYBIND11_STDLIB=\"_libstdcpp\" -DPYBIND11_BUILD_ABI=\"_cxxabi1011\" -isystem /home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/include -isystem /home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/include/torch/csrc/api/include -isystem /home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/include/TH -isystem /home/ning/anaconda3/envs/chatpose/lib/python3.9/site-packages/torch/include/THC -isystem /usr/local/cuda/include -isystem /home/ning/anaconda3/envs/chatpose/include/python3.9 -D_GLIBCXX_USE_CXX11_ABI=0 -D__CUDA_NO_HALF_OPERATORS__ -D__CUDA_NO_HALF_CONVERSIONS__ -D__CUDA_NO_BFLOAT16_CONVERSIONS__ -D__CUDA_NO_HALF2_OPERATORS__ --expt-relaxed-constexpr -gencode=arch=compute_75,code=sm_75 -gencode=arch=compute_86,code=compute_86 -gencode=arch=compute_86,code=sm_86 --compiler-options '-fPIC' -std=c++14 -ccbin=$(which gcc-7) -c /home/ning/Desktop/get_skeleton_test/ChatPose-PoseGPT/model/smpl/rasterizer/standard_rasterize_cuda_kernel.cu -o standard_rasterize_cuda_kernel.cuda.o 
No such file or directory
nvcc fatal   : Failed to preprocess host compiler properties.
ninja: build stopped: subcommand failed.
```
- 看上去是一个名叫`standard_rasterize_cuda`加速插件的编译失败了, 它需要依赖 `gcc-7`, 然后执行`which gcc-7` 的时候因为返回值异常直接崩掉了.

尝试解决:
- 安装 gcc-7
    - 参考教程: https://www.cnblogs.com/ywb123/p/16660711.html

---

跑代码issue:
- 直接用 bf16 精度没办法在 3090 上跑, 会OOM.
- 改成 fp16, 则报错`AttributeError: 'LlamaAttention' object has no attribute 'rope_theta'`