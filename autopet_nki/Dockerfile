#FROM python:3.9-slim
FROM pytorch/pytorch


RUN groupadd -r algorithm && useradd -m --no-log-init -r -g algorithm algorithm

RUN mkdir -p /opt/algorithm /input /output \
    && chown algorithm:algorithm /opt/algorithm /input /output

USER algorithm

WORKDIR /opt/algorithm

ENV PATH="/home/algorithm/.local/bin:${PATH}"

RUN python -m pip install --user -U pip

COPY --chown=algorithm:algorithm requirements.txt /opt/algorithm/
COPY --chown=algorithm:algorithm nnFormer/ /opt/algorithm/

#RUN ls -la /opt/algorithm/*
#RUN ls -la /opt/algorithm/nnFormer/*

RUN python -m pip install --user torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cu113
RUN python -m pip install --user -rrequirements.txt
#RUN python -m pip install -e . --user nnFormer

COPY --chown=algorithm:algorithm process.py /opt/algorithm/
COPY --chown=algorithm:algorithm predict.py /opt/algorithm/

RUN mkdir -p /opt/algorithm/nnFormer_trained_models/nnFormer/

# Store your weights in the container
COPY --chown=algorithm:algorithm weights_nnFormer_fullres.zip /opt/algorithm/nnFormer_trained_models/nnFormer/
RUN python -c "import zipfile; zipfile.ZipFile('/opt/algorithm/nnFormer_trained_models/nnFormer/weights_nnFormer_fullres.zip').extractall('/opt/algorithm/nnFormer_trained_models/nnFormer/')"

# nnUNet specific setup
RUN mkdir -p /opt/algorithm/nnFormer_raw/nnFormer_raw_data/Task501_AutopetFull/imagesTs
RUN mkdir -p /opt/algorithm/nnFormer_raw/nnFormer_raw_data/Task501_AutopetFull/result

ENV nnFormer_raw_data_base="/opt/algorithm/nnFormer_raw"
ENV RESULTS_FOLDER="/opt/algorithm/nnFormer_trained_models"
ENV MKL_SERVICE_FORCE_INTEL=1


ENTRYPOINT python -m process $0 $@

