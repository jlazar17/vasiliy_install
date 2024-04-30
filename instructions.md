# Let's get LeptonWeighter installed

## Get our environment and directories set up. You can change things in `env.sh`, but I think the things in here should be internally consistent.

```bash
source env.sh
mkdir $PREFIX
mkdir $SRCDIR
```

## Download all the dependencies that we will need


```bash
cd $SRCDIR
git clone https://github.com/arguelles/nuSQuIDS.git
git clone https://github.com/jsalvado/SQuIDS.git
git clone https://github.com/icecube/LeptonWeighter.git
git clone https://github.com/icecube/photospline.git
git clone https://github.com/icecube/nuflux.git
wget https://heasarc.gsfc.nasa.gov/FTP/software/fitsio/c/cfitsio_latest.tar.gz
tar -xvf cfitsio_latest.tar.gz
```

## Install `SQuIDS`

```bash
cd $SRCDIR/SQuIDS/
./configure --prefix=$PREFIX
make
make install
export PKG_CONFIG_PATH=$PREFIX/lib/pkgconfig
```

## Install `nuSQuIDS`

```bash
cd $SRCDIR/nuSQuIDS
./configure --prefix=$PREFIX
make
make install
```

## Install `CFITSIO`

```bash
cd $SRCDIR/cfitsio-4.4.0
./configure --prefix=$PREFIX
make
make install
```

## Install `photospline`

```bash
cd $SRCDIR/photospline
cmake . -DCMAKE_INSTALL_PREFIX=$PREFIX
make install
```

## Install `nuflux`

Here is where things get hacky.

```bash
cd $SRCDIR/nuflux
```

First we need to make some modifications to some of the build files. In `src/library/meson.build`, I needed to change line 15 so that `detail.cpp` is in square brackets, __i.e.__ `lib_src + 'detail.cpp'` -> `lib_src + ['detail.cpp']`. It is possible that this "fix" if meson-version dependent. Next, we are going to comment out the last four lines in `meson.build`, __i.e.__ lines 26-29. You should definitely do this.

Now we can do the build

```bash
meson --prefix=$PREFIX build
ninja -C build
ninja -C build install
./configure --prefix=$PREFIX --with-photospline-config=$PREFIX/bin/photospline-config --python-module-dir=$PREFIX/lib
make
make install
make python
make python-install
