ARG from=ubuntu:jammy
FROM ${from}

# Change default shell to bash. This is effective only in the Dockerfile.
SHELL ["/bin/bash", "-i", "-c"]

# System utilities
RUN apt-get update &&\
    apt-get install -y --no-install-recommends \
        ca-certificates \
        wget \
        nano \
        bzip2 \
        &&\
    rm -rf /var/lib/apt/lists/*

# Configure conda
ENV CONDA_PREFIX=/conda
ARG CONDA_PYTHON_VERSION=3.10
ENV MAMBA_ROOT_PREFIX=$CONDA_PREFIX/.mamba

# Install micromamba and create conda environment
RUN cd /usr/local &&\
    wget -qO- https://micromamba.snakepit.net/api/micromamba/linux-64/latest \
        | tar -xvj bin/micromamba &&\
    eval "$(micromamba shell hook -s bash)" &&\    
    micromamba create -y -p $CONDA_PREFIX "python==$CONDA_PYTHON_VERSION.*" mamba -c conda-forge &&\
    micromamba activate $CONDA_PREFIX &&\
    conda config --system --add channels conda-forge &&\
    conda config --system --set channel_priority strict &&\
    conda clean -afy

# Enable by default the conda environment for all users
RUN echo 'function activate_conda() {' >> /etc/bash.bashrc &&\
    echo '  eval "$(micromamba shell hook -s bash)"' >> /etc/bash.bashrc &&\
    echo '  micromamba activate $CONDA_PREFIX' >> /etc/bash.bashrc &&\
    echo '}' >> /etc/bash.bashrc &&\
    echo '[[ -z $NO_CONDA ]] && activate_conda' >> /etc/bash.bashrc

# Install CUDA Toolkit
ARG cudatoolkit_version="11.2.*"
RUN echo "cudatoolkit $cudatoolkit_version" >> $CONDA_PREFIX/conda-meta/pinned &&\
    mamba install -y cudatoolkit cuda-nvcc -c nvidia -c conda-forge &&\
    conda clean -afy

# Allow installing CUDA packages also if no GPU is detected in Dockerfile:
# https://conda-forge.org/docs/user/tipsandtricks.html#installing-cuda-enabled-packages-like-tensorflow-and-pytorch
ARG CONDA_OVERRIDE_CUDA="11.2"

# Application dependencies
RUN echo "jaxlib = * = cuda112*" >> $CONDA_PREFIX/conda-meta/pinned &&\
    mamba install -y \
        diffrax \
        ipython \
        jax \
        jupyter \
        matplotlib \
        numpy \
        optax \
        scipy \
        &&\
    conda clean -afy &&\
    # Install pip packages preventing to upgrade jax
    jax_version="$(pip list | grep '^jax ' | tr -s ' ' | cut -d ' ' -f 2)" &&\
    pip install "jax==${jax_version}" jax_dataclasses jaxlie &&\
    rm -rf $HOME/.cache/pip

# Print the full conda environment for reproducibility purpose
RUN mamba env export --no-builds | grep -v "^prefix"

# Configure Jupyter
ENV JUPYTER_PATH="/jupyter"
ENV JUPYTER_DATA_DIR="/tmp"

# Configure Nvidia support
ENV NVIDIA_VISIBLE_DEVICES=all
ENV NVIDIA_DRIVER_CAPABILITIES=all

CMD ["bash", "-ci", "jupyter notebook --notebook-dir $JUPYTER_PATH"]
