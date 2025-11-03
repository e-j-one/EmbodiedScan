# FROM nvidia/cuda:12.0.1-devel-ubuntu20.04
FROM nvidia/cuda:11.3.1-devel-ubuntu20.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl ca-certificates bzip2 git \
    build-essential libopenblas-dev \
    libgl1-mesa-glx libglib2.0-0 libsm6 libxrender1 libxext6 \
 && rm -rf /var/lib/apt/lists/*

# Install Miniconda
ENV CONDA_DIR=/opt/conda
RUN curl -fsSL https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -o /tmp/miniconda.sh \
 && bash /tmp/miniconda.sh -b -p "$CONDA_DIR" \
 && rm -f /tmp/miniconda.sh \
 && "$CONDA_DIR/bin/conda" config --system --set always_yes true \
 && "$CONDA_DIR/bin/conda" clean -a -q -y

# PATH setup
ENV PATH="$CONDA_DIR/bin:$PATH"
RUN conda config --system --set auto_activate_base false

# Accept ToS (no --yes flag)
RUN conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main \
 && conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r

SHELL ["/bin/bash", "-lc"]

# Create env and install dependencies
RUN conda create -y -n embodiedscan python=3.8.18 \
 && conda install -y -n embodiedscan -c pytorch \
      pytorch==1.11.0 torchvision==0.12.0 torchaudio==0.11.0 cudatoolkit=11.3 \
 && conda clean -a -y


# Make builds more compatible with old numpy.distutils
ENV SETUPTOOLS_USE_DISTUTILS=stdlib \
    PIP_NO_BUILD_ISOLATION=1

# Make sure conda is initialized for bash shells
RUN conda init bash

# Ensure the environment auto-activates on container start
RUN echo "source activate embodiedscan" >> ~/.bashrc

# Set working directory
WORKDIR /workspace

# Default command
CMD ["/bin/bash"]
