ARG PYTORCH="1.13.1"
ARG CUDA="11.6"
ARG CUDNN="8"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel

ENV TORCH_CUDA_ARCH_LIST="8.0 8.6 8.7"
ENV TORCH_NVCC_FLAGS="-Xfatbin -compress-all"
ENV CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"

# To fix GPG key error when running apt-get update
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub

RUN apt-get update && apt-get install -y ffmpeg libsm6 libxext6 git ninja-build libglib2.0-0 libsm6 libxrender-dev libxext6 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install openmim
RUN pip install --no-cache-dir -U openmim
# Install mmengine, mmcv, and mmdetection
RUN mim install --no-cache-dir "mmengine==0.6.0" "mmcv==2.0.0rc4" "mmdet==3.0.0rc6"
# Install MMRotate
RUN conda clean --all -y
RUN git clone https://github.com/open-mmlab/mmrotate.git -b dev-1.x /mmrotate
WORKDIR /mmrotate
ENV FORCE_CUDA="1"
RUN pip install -r requirements/build.txt
RUN pip install --no-cache-dir -e .
RUN pip install "yapf==0.40.1" "numpy<2.0" "albumentations==1.3.1"
RUN apt update
RUN apt install -y tmux silversearcher-ag nano vim
RUN pip install tensorboard
RUN git clone --recursive -b main https://github.com/open-mmlab/mmdeploy.git
WORKDIR /mmrotate/mmdeploy
RUN python3 tools/scripts/build_ubuntu_x64_ort.py
ENV PYTHONPATH="/mmrotate/mmdeploy/build/lib:$PYTHONPATH"
ENV LD_LIBRARY_PATH="/mmrotate/mmdeploy-dep/onnxruntime-linux-x64-1.8.1/lib/:$LD_LIBRARY_PATH"
RUN pip install onnxruntime

