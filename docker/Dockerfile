FROM ubuntu:20.04
FROM python:3.8 

# Set default UID and GID
ENV USER_ID 1000
ENV GROUP_ID 1000

# Set an encoding to make things work smoothly.
ENV LANG en_US.UTF-8

# create user and install latest miniconda
COPY docker/requirements.txt /tmp/requirements.txt
COPY docker/requirements.txt.forge /tmp/requirements_forge.txt
RUN set -ex \
 && groupadd -r conda -g $GROUP_ID \
 && useradd -u $USER_ID -r -g conda -d /home/conda -s /bin/bash -m conda \
 && chmod 755 /home/conda \
 && echo 'conda ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers \
 && curl -sSL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o miniconda.sh \
 && bash miniconda.sh -b -p /opt/conda \
 && rm miniconda.sh \
 && touch /opt/conda/conda-meta/pinned \
 && export PATH=/opt/conda/bin:$PATH \
 && conda config --set show_channel_urls True \
 && conda update --all --yes \
 && conda install --yes --file /tmp/requirements.txt \
 && conda install --yes --channel conda-forge --file /tmp/requirements_forge.txt \
 && conda clean -tipsy \
 && rm -rf /opt/conda/pkgs \
 && rm /tmp/requirements.txt \
 && rm /tmp/requirements_forge.txt

# set default user and workdir
WORKDIR /home/conda
ADD dist/proteus-0.1.tar.gz .

USER root
RUN mkdir -p proteus-0.1/build
RUN export PATH=/opt/conda/bin:$PATH
WORKDIR /home/conda/proteus-0.1
RUN python3 setup.py install
WORKDIR /home/conda

RUN mkdir -p runconfig && chown conda:conda runconfig
RUN mkdir -p input_dir && chown conda:conda input_dir
RUN mkdir -p output_dir && chown conda:conda output_dir
RUN mkdir -p scratch_dir && chown conda:conda scratch_dir && chmod 777 scratch_dir

USER conda

ENV PROJ_LIB /opt/conda/share/proj/
ENV GDAL_DIR /opt/conda
ENV PATH $GDAL_DIR/bin:$PATH
ENV GDAL_DATA $GDAL_DIR/share/gdal

ENV PYTHONPATH /home/conda/proteus-0.1/src/:$PYTHONPATH
ENV PATH /home/conda/proteus-0.1/bin/:$PATH

CMD [ "/bin/bash" ]