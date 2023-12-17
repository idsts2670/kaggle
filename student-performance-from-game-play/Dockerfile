FROM --platform=linux/amd64 continuumio/miniconda3
# WORKDIR /tmp
# ADD environment.yml /tmp/environment.yml


RUN set -x
RUN apt update -y
RUN apt upgrade -y

RUN apt install -y wget \
    sudo \
    vim

RUN conda update -y conda
RUN conda install -y pandas \
    matplotlib \
    seaborn \
    scikit-learn \
    numpy \
    pytorch \
    lightgbm \
    python-dateutil \
    openpyxl \
    boto3 \
    jupyter

RUN pip install --no-cache-dir -U polars

# RUN conda env create -f /tmp/environment.yml
# RUN conda install --no-cache-dir --upgrade -r /code/requirements.txt