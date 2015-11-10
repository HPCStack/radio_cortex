FROM ubuntu:14.04

MAINTAINER speransky@gmail.com
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update
RUN apt-get install -y build-essential libedit2 libglu1-mesa-dev libgmp3-dev zlib1g-dev 
RUN apt-get install -y freeglut3-dev wget ncurses-dev libcurl4-gnutls-dev git autoconf subversion 
RUN apt-get install -y libtool fftw3-dev

RUN wget -c launchpadlibrarian.net/70575439/libgmp3c2_4.3.2%2Bdfsg-2ubuntu1_amd64.deb
RUN dpkg -i libgmp3c2_4.3.2*.deb

RUN wget -O ghc.tar.bz2 http://www.haskell.org/ghc/dist/7.8.3/ghc-7.8.3-x86_64-unknown-linux-deb7.tar.bz2
RUN tar xvfj ghc.tar.bz2
RUN cd ghc-7.8.3 && ./configure
RUN cd ghc-7.8.3 && make install
RUN rm -rf ghc.tar.bz2 ghc-7.8.3

RUN wget -O cabal.tar.gz http://hackage.haskell.org/package/cabal-install-1.20.0.1/cabal-install-1.20.0.1.tar.gz
RUN tar xvfz cabal.tar.gz
RUN cd cabal-install-1.20.0.1 && ./bootstrap.sh
RUN rm -rf cabal-install-1.20.0.1 cabal.tar.gz
ENV PATH /.cabal/bin:$PATH
ENV CUDA_RUN http://developer.download.nvidia.com/compute/cuda/6_5/rel/installers/cuda_6.5.14_linux_64.run

RUN apt-get update && apt-get install -q -y \
  wget \
  build-essential 

RUN cd /opt && \
  wget $CUDA_RUN && \
  chmod +x *.run && \
  mkdir nvidia_installers && \
  ./cuda_6.5.14_linux_64.run -extract=`pwd`/nvidia_installers && \
  cd nvidia_installers && \
  ./NVIDIA-Linux-x86_64-340.29.run -s -N --no-kernel-module

RUN cd /opt/nvidia_installers && \
  ./cuda-linux64-rel-6.5.14-18749181.run -noprompt

ENV LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-6.5/lib64
ENV PATH=$PATH:/usr/local/cuda-6.5/bin
ENV PATH /root/.cabal/bin:$PATH
RUN PATH=/root/.cabal/bin cabal update && cabal install cabal-install && cabal install alex && cabal install happy && cabal install c2hs-0.26.1 && cabal install cuda
RUN echo "/usr/local/cuda-6.5/extras/CUPTI/lib64" >>/etc/ld.so.conf.d/cuda.conf && \
	 echo "/usr/local/cuda-6.5/lib64" >>/etc/ld.so.conf.d/cuda.conf && \
	 ldconfig
RUN git clone https://github.com/SKA-ScienceDataProcessor/RC 
ADD Logging.hs /RC/MS4/lib/DNA/Logging.hs
ADD dna-ms4.cabal /RC/MS4/dna-ms4.cabal
RUN cd /RC/MS4 && \
	 sh ./boot.sh && \
  	cabal install
CMD "/RC/MS4/bin/ddp-in-memory"

