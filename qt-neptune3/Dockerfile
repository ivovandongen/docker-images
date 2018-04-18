FROM ubuntu:16.04

# --------------------------------------------------------------------------------------------------
# Install common packages

RUN set -eu \
 && apt-get update \
 && apt-get -y install git make locales \
     clang-3.9 clang-4.0 clang-5.0 \
     g++-5 \
     python python-pip \
     ccache cmake

# --------------------------------------------------------------------------------------------------
# Setup environment

RUN locale-gen en_US.UTF-8

ENV LC_ALL="en_US.UTF-8" \
    CC="gcc-5" \
    CXX="g++-5"\
    PATH=/usr/lib/ccache:$PATH \
    WORK_DIR=/work

ENV SRC_DIR=${WORK_DIR}/src \
    BUILD_DIR=${WORK_DIR}/build \
    INSTALL_DIR=${WORK_DIR}/install

RUN mkdir -p $SRC_DIR \
        && mkdir -p $BUILD_DIR \
        && mkdir -p $INSTALL_DIR


# Build Qt5
RUN git clone git://code.qt.io/qt/qt5.git $SRC_DIR/qt5

RUN cd $SRC_DIR/qt5 \
    && ./init-repository  --branch --module-subset=essential,addon,preview

RUN apt-get -y install libglfw3-dev libgles2-mesa-dev libssl-dev

RUN mkdir $BUILD_DIR/qt5 \
    && cd $BUILD_DIR/qt5 \
    && $SRC_DIR/qt5/configure -opensource -confirm-license -nomake examples -nomake tests \
    -ssl -opengl es2 -ccache \
    -skip qtandroidextras -skip qtdatavis3d -skip qtcharts -skip qtdocgallery -skip qtenginio -skip qtfeedback \
    -skip qtpim -skip qtpurchasing -skip qtquick1 -skip qtscript -skip qtspeech -skip qtwebchannel -skip qtwebengine \
    -skip qtwebglplugin -skip qtwebview \
    -prefix $INSTALL_DIR/qt5

RUN cd $BUILD_DIR/qt5 \
    && make -j 6

RUN cd $BUILD_DIR/qt5 \
    && make install -j 6

ENV PATH=$INSTALL_DIR/qt5/bin/:$PATH

# Build qtivi
RUN git clone git://code.qt.io/qt/qtivi.git $SRC_DIR/qtivi \
    && cd $SRC_DIR/qtivi \
    && git submodule init && git submodule update

RUN apt-get -y install python3 python3-virtualenv

RUN mkdir $BUILD_DIR/qtivi \
    && cd $BUILD_DIR/qtivi \
    && qmake $SRC_DIR/qtivi/

RUN cd $BUILD_DIR/qtivi \
    && make -j 6
RUN cd $BUILD_DIR/qtivi \
    && make install

# Build Qt wayland
# (can be left out for now as we use single process)

RUN git clone git://code.qt.io/qt/qtwayland.git $SRC_DIR/qtwayland \
        && cd $SRC_DIR/qtwayland \
        && git checkout 5.11

RUN mkdir $BUILD_DIR/qtwayland \
    && cd $BUILD_DIR/qtwayland \
    && qmake $SRC_DIR/qtwayland

RUN cd $BUILD_DIR/qtwayland \
    && make -j 6
RUN cd $BUILD_DIR/qtwayland \
    && make install


# Build qtapplicationmanager
RUN git clone git://code.qt.io/qt/qtapplicationmanager.git $SRC_DIR/qtapplicationmanager

RUN mkdir $BUILD_DIR/qtapplicationmanager \
    && cd $BUILD_DIR/qtapplicationmanager \
    && qmake $SRC_DIR/qtapplicationmanager/ -config force-single-process #for now force single process

RUN cd $BUILD_DIR/qtapplicationmanager \
    && make -j 6
RUN cd $BUILD_DIR/qtapplicationmanager \
    && make install

# Build neptune ui

RUN apt-get -y install curl software-properties-common \
        && add-apt-repository ppa:git-core/ppa

RUN curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | bash \
        && apt-get -y install git-lfs \
        && git lfs install


RUN git clone git://code.qt.io/qt-apps/neptune3-ui.git $SRC_DIR/neptune3-ui \
        && cd $SRC_DIR/neptune3-ui \
        && git config credential.helper cache \
        && git config http.sslverify false

RUN mkdir $BUILD_DIR/neptune3-ui \
    && cd $BUILD_DIR/neptune3-ui \
    && qmake INSTALL_PREFIX=$INSTALL_DIR/neptune3-ui $SRC_DIR/neptune3-ui

RUN cd $BUILD_DIR/neptune3-ui \
        && make -j 6 && make install

WORKDIR $WORK_DIR

