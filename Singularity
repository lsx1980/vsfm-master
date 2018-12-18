BootStrap: docker
From: ubuntu:16.04

%help
  Help will go here

%labels
  Maintainer Chris Cotter
  Version v0.01

%post
  # Install dependencies
  apt update
  apt install -y \
      git \
      wget \
      build-essential \
      unzip \
      libgtk2.0-dev \
      libglew-dev \
      libdevil-dev \
      libboost-all-dev \
      libatlas-cpp-0.6-dev \
      libatlas-dev \
      imagemagick \
      libatlas3-base \
      libcminpack-dev \
      libgfortran3 \
      libmetis-edf-dev \
      libparmetis-dev \
      freeglut3-dev \
      libgsl-dev \
      glew-utils \
      libblas-dev \
      liblapack-dev \


  # Download and extract VisualSFM
  # http://ccwu.me/vsfm/index.html

  cd /opt
  wget http://ccwu.me/vsfm/download/VisualSFM_linux_64bit.zip
  unzip VisualSFM_linux_64bit.zip
  rm VisualSFM_linux_64bit.zip

  # Download and install SiftGPU in the appropriate directory
  # https://github.com/pitzer/SiftGPU

  cd /opt/vsfm
  wget https://github.com/pitzer/SiftGPU/archive/master.zip
  unzip master.zip
  rm master.zip
  mv SiftGPU-master SiftGPU

  cd /opt/vsfm/SiftGPU
  make
  cp /opt/vsfm/SiftGPU/bin/libsiftgpu.so /opt/vsfm/bin

  # Download and install Multicore Bundle Adjustment ('pba') in the appropriate directory
  # http://grail.cs.washington.edu/projects/mcba/

  cd /opt/vsfm
  wget http://grail.cs.washington.edu/projects/mcba/pba_v1.0.5.zip
  unzip pba_v1.0.5.zip
  rm pba_v1.0.5.zip

  cd /opt/vsfm/pba
  # Try using the following commands if the make command failed
  # echo -e "#include <stdlib.h>\n$(cat ~/vsfm/pba/src/pba/SparseBundleCU.h)" > ~/vsfm/pba/src/pba/SparseBundleCU.h
  # echo -e "#include <stdlib.h>\n$(cat ~/vsfm/pba/src/pba/pba.h)" > ~/vsfm/pba/src/pba/pba.h
  #WITH CUDA
  #make
  #cp /opt/vsfm/pba/bin/libpba.so /opt/vsfm/vsfm/bin/
  #WITHOUT CUDA
  mv makefile makefile_with_gpu
  mv makefile_no_gpu makefile
  make

  # Download, hack and install PMVS in the appropriate directory
  # http://www.di.ens.fr/pmvs/documentation.html
  cd /opt/vsfm
  wget http://www.di.ens.fr/pmvs/pmvs-2.tar.gz
  tar xvzf pmvs-2.tar.gz
  rm pmvs-2.tar.gz

  cd /opt/vsfm/pmvs-2/program/main/
  cp /opt/vsfm/pmvs-2/program/main/mylapack.o /opt/vsfm/pmvs-2/program/main/mylapack.o.backup
  make clean
  cp /opt/vsfm/pmvs-2/program/main/mylapack.o.backup /opt/vsfm/pmvs-2/program/main/mylapack.o
  make depend
  make


  # Download and install Graclus1.2
  # http://www.cs.utexas.edu/users/dml/Software/graclus.html

  cd /opt/vsfm
  wget http://www.cs.utexas.edu/users/dml/Software/graclus1.2.tar.gz
  tar xvzf graclus1.2.tar.gz
  rm graclus1.2.tar.gz
  sed -i 's/COPTIONS = -DNUMBITS=32/COPTIONS = -DNUMBITS=64/' /opt/vsfm/graclus1.2/Makefile.in
  cd /opt/vsfm/graclus1.2
  make


  # Download, hack and install CMVS in the appropriate directory
  # http://www.di.ens.fr/cmvs/documentation.html
  cd /opt/vsfm
  wget http://www.di.ens.fr/cmvs/cmvs-fix2.tar.gz
  tar xvzf cmvs-fix2.tar.gz
  rm cmvs-fix2.tar.gz

  cp /opt/vsfm/pmvs-2/program/main/mylapack.o /opt/vsfm/cmvs/program/main/

  sed -i '1s/^/#include <vector>\n#include <numeric>\n/' /opt/vsfm/cmvs/program/base/cmvs/bundle.cc
  sed -i '1s/^/#include <stdlib.h>\n/' /opt/vsfm/cmvs/program/main/genOption.cc
  sed -e '/Your INCLUDE path*/ s/^#*/#/' -i /opt/vsfm/cmvs/program/main/Makefile
  sed -e '/Your metis directory*/ s/^#*/#/' -i /opt/vsfm/cmvs/program/main/Makefile
  sed -e '/Your LDLIBRARY path*/ s/^#*/#/' -i /opt/vsfm/cmvs/program/main/Makefile

  sed -i "s:YOUR_INCLUDE_METIS_PATH =*:YOUR_INCLUDE_METIS_PATH = -I/opt/vsfm/graclus1.2/metisLib:" /opt/vsfm/cmvs/program/main/Makefile
  sed -i "s:YOUR_LDLIB_PATH =*:YOUR_LDLIB_PATH = -L/opt/vsfm/graclus1.2:" /opt/vsfm/cmvs/program/main/Makefile


  cd /opt/vsfm/cmvs/program/main
  make depend
  make
  cp /opt/vsfm/cmvs/program/main/cmvs /opt/vsfm/bin
  cp /opt/vsfm/cmvs/program/main/pmvs2 /opt/vsfm/bin
  cp /opt/vsfm/cmvs/program/main/genOption /opt/vsfm/bin


  cd /opt/vsfm
  make
  chmod a+x /opt/vsfm/bin/VisualSFM
  
%environment
  PATH=$PATH:/opt/vsfm/bin/
  export PATH
  LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/vsfm/bin/
  export LD_LIBRARY_PATH

%runscript
  /opt/vsfm/bin/VisualSFM "$@"
