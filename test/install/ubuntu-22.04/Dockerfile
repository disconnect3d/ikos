FROM ubuntu:22.04
MAINTAINER Maxime Arthaud <maxime.arthaud@nasa.gov>
ARG njobs=2
ARG build_type=Release

# Upgrade
RUN apt-get update
RUN apt-get upgrade -y

# Add ppa for llvm 14.0
RUN echo "deb http://apt.llvm.org/jammy/ llvm-toolchain-jammy-14 main" >> /etc/apt/sources.list

# Add llvm repository key
RUN apt-get install -y wget gnupg
RUN wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add -

# Refresh cache
RUN apt-get update

# Install all dependencies
RUN apt-get install -y gcc g++ cmake doxygen libgmp-dev libboost-dev \
        libboost-filesystem-dev libboost-thread-dev libboost-test-dev \
        python3 python3-distutils python3-pygments \
        libsqlite3-dev libtbb-dev libz-dev libedit-dev \
        llvm-14 llvm-14-dev llvm-14-tools clang-14

# Add ikos source code
ADD . /root/ikos

# Build ikos
RUN rm -rf /root/ikos/build && mkdir /root/ikos/build
WORKDIR /root/ikos/build
ENV MAKEFLAGS "-j$njobs"
RUN cmake \
        -DCMAKE_INSTALL_PREFIX="/opt/ikos" \
        -DCMAKE_BUILD_TYPE="$build_type" \
        -DLLVM_CONFIG_EXECUTABLE="/usr/lib/llvm-14/bin/llvm-config" \
        ..
RUN make
RUN make install

# Run the tests
RUN make check

# Add ikos to the path
ENV PATH "/opt/ikos/bin:$PATH"

# Done
WORKDIR /
