FROM ghcr.io/taffish-org/conda:24.9.2

LABEL maintainer="taffish@163.com"

WORKDIR /root

COPY . .

SHELL ["/bin/bash", "-c"]

RUN apt-get update && apt-get install -y git wget libxml2-dev && \
    mkdir -p /esm-fold/install/ && \
    mv /root/conda.yml  /esm-fold/install/ && \
    mv /root/pip.txt    /esm-fold/install/ && \
    mv /root/test.fasta /esm-fold/install/ && \
    conda init bash && . /root/anaconda3/etc/profile.d/conda.sh && \
    cd /esm-fold/install/ && conda env create -f conda.yml && \
    conda activate esmfold && \
    conda install -y -c conda-forge gcc_linux-64=9.3.0 gxx_linux-64=9.3.0 && \
    ln -sf /root/anaconda3/envs/esmfold/bin/x86_64-conda-linux-gnu-gcc /root/anaconda3/envs/esmfold/bin/gcc && \
    ln -sf /root/anaconda3/envs/esmfold/bin/x86_64-conda-linux-gnu-g++ /root/anaconda3/envs/esmfold/bin/g++ && \
    cd /esm-fold/install/ && \
    pip install -r pip.txt && \
    pip install fair-esm && \
    pip install git+https://github.com/facebookresearch/esm.git && \
    pip install "fair-esm[esmfold]" && \
    pip install 'dllogger @ git+https://github.com/NVIDIA/dllogger.git' && \
    conda uninstall -y mkl && \
    conda install -y mkl=2018 -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/linux-64/ && \
    pip uninstall -y torch torchvision torchaudio && \
    pip install torch==1.12.1+cu113 torchvision==0.13.1+cu113 torchaudio==0.12.1 -f https://download.pytorch.org/whl/cu113/torch_stable.html && \
    cd /esm-fold/install/ && \
    git clone --filter=blob:none --quiet https://github.com/aqlaboratory/openfold.git ./openfold && \
    cd /esm-fold/install/openfold/ && \
    git rev-parse -q --verify '4b41059694619831a7db195b7e0988fc4ff3a307' && \
    git fetch -q https://github.com/aqlaboratory/openfold.git 4b41059694619831a7db195b7e0988fc4ff3a307 && \
    git checkout -q 4b41059694619831a7db195b7e0988fc4ff3a307 && \
    sed -i 's/arch=compute_{major}{minor},code=sm_{major}{minor}/arch=compute_70,code=sm_70/g' ./setup.py && \
    pip install -e .

RUN conda init bash && . /root/anaconda3/etc/profile.d/conda.sh && conda activate esmfold && \
    cd /esm-fold/install/ && \
    esm-fold -i ./test.fasta -o ./ || true

ENV PATH=/root/anaconda3/envs/esmfold/bin:${PATH}

ENV TAFFISH_ENV=TAFFISH
ENV TAFFISH_NAME=esm-fold
