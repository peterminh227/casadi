![Github All Releases](https://img.shields.io/github/downloads/casadi/casadi/total.svg)

Learn all about CasADi at the [homepage](http://casadi.org) or jump to [install instructions](http://install.casadi.org)...

CasADi's MATLAB front-end uses an extension of SWIG - the software used to generate CasADi's Python front-end. The work on extending the SWIG to support MATLAB is a collaborate effort and we refer to the swig-devel mailing list (cf. this page) for detailed discussions on how this work is progressing.

# Compile source CasAdi on Ubuntu 16.04 

* Get essential package: (*bison for swig, )

```
  sudo apt-get install bison -y
  sudo apt-get install byacc -y
  sudo apt-get install -y binutils gcc g++ gfortran git cmake liblapack-dev ipython
  sudo apt-get install -y python-dev python-numpy python-scipy python-matplotlib libmumps-seq-dev
  sudo apt-get install -y libblas-dev liblapack-dev libxml2-dev
  sudo apt-get install -y libpcre3-dev automake yodl
```

* Compile IPOPT from source

```
svn co https://projects.coin-or.org/svn/Ipopt/stable/3.12 CoinIpopt
cd CoinIpopt
# get Thirdparty
cd CoinIpopt/Thirdparty
cd Blas && ./get.Blas && cd ..
cd Lapack && ./get.Lapack && cd ..
cd Metis && ./get.Metis && cd ..
cd Mumps && ./get.Mumps && cd .. (Sparse direct linear solver with permissive license)
cd ..; mkdir build; cd build

# configuration, --prefix = <installation location> = /opt/casadi-linux-matlab/
 ../configure coin_skip_warn_cxxflags=yes --prefix=/opt/casadi-linux-matlab/ipopt-install --disable-shared ADD_FFLAGS=-fPIC ADD_CFLAGS=-fPIC ADD_CXXFLAGS=-fPIC --with-blas=BUILD --with-lapack=BUILD --with-mumps=BUILD --with-metis=BUILD
sudo make
sudo make install
cd ../../
# check the static library in the installation directory /opt/casadi-linux-matlab/ipopt-install
```

* Compile swig which is supported for Matlab
```
git clone --depth=1 https://github.com/jaeandersson/swig.git
cd swig
./autogen.sh
./configure --prefix=/opt/casadi-linux-matlab/swig-install
sudo make
sudo make install
cd ..
```

* Export PATHs
```
export SWIG_HOME="/opt/casadi-linux-matlab/swig-install"
export PATH="$SWIG_HOME/bin:$SWIG_HOME/share:$PATH"
export PKG_CONFIG_PATH="$PKG_CONFIG_PATH:/opt/casadi-linux-matlab/ipopt-install/lib/pkgconfig"
```

* Compile CasAdi 

```
# Clone the newest update
git clone https://github.com/casadi/casadi.git -b master casadi
cd casadi
mkdir build && cd build
cmake -DCMAKE_INSTALL_PREFIX=/opt/casadi-linux-matlab/casadi-install -DWITH_THREAD=ON -DWITH_COMMON=ON -DINSTALL_INTERNAL_HEADERS=ON -DWITH_IPOPT=ON -DWITH_OPENMP=ON -DWITH_SELFCONTAINED=ON -DWITH_DEEPBIND=ON -DWITH_MATLAB=ON ..
sudo make
sudo make install
```
* Compile HSL from source:
```
# get HSL source code <http://www.hsl.rl.ac.uk/ipopt/> extract to /home/<user>/coinhsl
# get Metis
wget http://glaros.dtc.umn.edu/gkhome/fetch/sw/metis/OLD/metis-4.0.3.tar.gz
tar -xvf metis-4.0.3.tar.gz
mv metis-4.0.3 ../coinhsl
cd ../coinhsl
./configure --prefix=/opt/coinhsl/ LIBS="-llapack" --with-blas="-L/usr/lib -lblas" CXXFLAGS="-g -O2 -fopenmp" FCFLAGS="-g -O2 -fopenmp" CFLAGS="-g -O2 -fopenmp"
sudo make
sudo make install
# create LINKER
sudo ln -s opt/coinhsl/lib/libcoinhsl.so opt/coinhsl/lib/libhsl.so
```
* Export PATHs before using CasAdi
```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/casadi-linux-matlab/casadi-install/casadi
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/casadi-linux-matlab/ipopt-install/lib
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/coinhsl/lib

```
