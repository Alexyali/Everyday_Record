# How to build lsquic

## git clone lsquic

```
$ git clone https://github.com/litespeedtech/lsquic.git
$ cd lsquic
$ git submodule update --init --recursive
```

## build boringssl 

```
# if not install libunwind
$ sudo apt install libunwind-dev
$ git clone https://github.com/google/boringssl.git
$ cd boringssl
$ git checkout b117a3a0b7bd11fe6ebd503ec6b45d6b910b41a1
$ mkdir build
$ cd build
$ cmake ..
$ make
```

## build lsquic

modify CMakeLists.txt in `lsquic/`

```
# add line 143
SET(BORINGSSL_DIR "${PROJECT_SOURCE_DIR}/boringssl")
# modify line 170
PATHS ${BORINGSSL_DIR}/build/${LIB_NAME}
```

build in `lsquic/`

```
# mkdir build
# cd build
# cmake ..
# make
```

