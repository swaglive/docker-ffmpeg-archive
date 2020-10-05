FROM        nvidia/cuda:10.2-devel as ffmpeg

ARG         NV_CODECS_HEADERS_VERSION=n10.0.26.0
ARG         FFMPEG_VERSION=n4.3
ENV         DEBIAN_FRONTEND=noninteractive

RUN         apt-get -y update && \
            apt-get install -y \
                build-essential \
                curl \
                yasm \
                nasm \
                pkg-config \
                autoconf \
                automake \
                cmake \
                libass-dev \
                libfreetype6-dev \
                libsdl2-dev \
                libtool \
                libva-dev \
                libvdpau-dev \
                libvorbis-dev \
                libxcb1-dev \
                libxcb-shm0-dev \
                libxcb-xfixes0-dev \
                texinfo \
                zlib1g-dev \
                libx264-dev \
                libx265-dev \
                libnuma-dev \
                libvpx-dev \
                libfdk-aac-dev \
                libmp3lame-dev \
                libwebp-dev \
                libopus-dev \
                libtheora-dev \
                libwavpack-dev \
                libwavpack1 \
                libxvidcore-dev \
                libvorbis-dev \
                libopenal-dev \
                libv4l-dev \
                libxml2-dev \
                librtmp-dev \
                libva-dev \
                libvdpau-dev \
                ocl-icd-opencl-dev \
                opencl-headers \
                libzmq5-dev

RUN         curl -sfL https://github.com/FFmpeg/nv-codec-headers/archive/$NV_CODECS_HEADERS_VERSION.tar.gz | tar xz
WORKDIR 	  nv-codec-headers-$NV_CODECS_HEADERS_VERSION
RUN         make -j$(nproc) && \
            make install -j$(nproc)

            # Compile and install ffmpeg from source
RUN 	  	  curl -sfL https://github.com/FFmpeg/FFmpeg/archive/$FFMPEG_VERSION.tar.gz | tar xz
WORKDIR 	  FFmpeg-$FFMPEG_VERSION

RUN 		    ./configure \
                --prefix=/usr/local \
                --extra-cflags=-I/usr/local/cuda/include \
                --extra-cflags=-I/usr/local/include \
                --extra-ldflags=-L/usr/local/cuda/lib64 \
                --disable-debug \
                --disable-stripping \
                --disable-static \
                --enable-shared \
                --enable-nonfree \
                # --enable-avisynth \
                --enable-swresample \
                --enable-avfilter \
                --enable-openal \
                --enable-fontconfig \
                --enable-gnutls \
                --enable-gpl \
                --enable-libass \
                --enable-libmp3lame \
                --enable-libvorbis \
                --enable-libvpx \
                --enable-libwebp \
                --enable-libxvid \
                --enable-libx264 \
                --enable-libx265 \
                --enable-libtheora \
                --enable-libv4l2 \
                --enable-postproc \
                --enable-pic \
                --enable-pthreads \
                --enable-libxcb \
                --enable-libxml2 \
                --enable-libopus \
                --enable-libfreetype \
                --enable-libfdk_aac \
                --enable-vaapi \
                --enable-vdpau \
                --enable-nvenc \
                --enable-cuda \
                --enable-cuvid \
                --enable-libnpp \
                --enable-opencl \
                --enable-cuda-nvcc \
                --enable-libzmq \
                --enable-libfontconfig

RUN         make -j$(nproc) && \
            make install -j$(nproc)

###

FROM        nvidia/cuda:11.1-runtime

ENV         LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
ENV         NVIDIA_DRIVER_CAPABILITIES compute,video,utility

ENTRYPOINT  ["ffmpeg"]

RUN         apt-get -y update && \
            apt-get install -y \
                libopenal1 \
                libxcb1 \
                libxcb-shm0 \
                libxcb-xfixes0 \
                libxcb-shape0 \
                libgl1 \
                libsdl2-2.0-0 \
                libv4l-0 \
                libxvidcore4 \
                libxv1 \
                libass9 \
                libva2 \
                libvpx6 \
                libwebp6 libwebpdemux2 libwebpmux3 \
                libfdk-aac1 \
                libmp3lame0 \
                libopus0 \
                libtheora0 \
                libx264-155 \
                libx265-179 \
                libva-drm2 \
                libva-x11-2 \
                ocl-icd-libopencl1 \
                libvdpau1 \
                libzmq5

            # FIX: Device creation failed: -19.
            # REF: https://github.com/microsoft/LightGBM/issues/586
RUN         mkdir -p /etc/OpenCL/vendors && \
            echo "libnvidia-opencl.so.1" > /etc/OpenCL/vendors/nvidia.icd

COPY        --from=ffmpeg /usr/local /usr/local
