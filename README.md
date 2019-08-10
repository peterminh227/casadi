![Github All Releases](https://img.shields.io/github/downloads/casadi/casadi/total.svg)

Learn all about CasADi at the [homepage](http://casadi.org) or jump to [install instructions](http://install.casadi.org)...

CasADi's MATLAB front-end uses an extension of SWIG - the software used to generate CasADi's Python front-end. The work on extending the SWIG to support MATLAB is a collaborate effort and we refer to the swig-devel mailing list (cf. this page) for detailed discussions on how this work is progressing.
* Minh - Wien 25 July 2019
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
 ../configure coin_skip_warn_cxxflags=yes --prefix=/opt/casadi-linux-matlab/ipopt-install --disable-shared CXX=g++ CC=gcc F77=gfortran ADD_FFLAGS="-fPIC -fopenmp -fexceptions" ADD_CFLAGS="-fPIC -fopenmp -fno-common -fexceptions" ADD_CXXFLAGS="-fPIC -fopenmp -fno-common -fexceptions" --with-blas=BUILD --with-lapack=BUILD --with-mumps=BUILD --with-metis=BUILD 
--> new
 ../configure coin_skip_warn_cxxflags=yes --prefix=/opt/casadi-linux-matlab/ipopt-install --disable-shared CXX=g++ CC=gcc F77=gfortran ADD_FFLAGS="-fPIC -fopenmp -fexceptions" ADD_CFLAGS="-fPIC -fopenmp -fno-common -fexceptions" ADD_CXXFLAGS="-fPIC -fopenmp -fno-common -fexceptions" --with-blas-lib="-L/opt/intel/mkl/lib/intel64/ -Wl,--no-as-needed -lmkl_intel_lp64 -lmkl_lapack95_lp64 -lmkl_sequential -lmkl_core -lgfortran -fopenmp -lpthread -lm -ldl" --with-lapack-lib="-L/opt/intel/mkl/lib/intel64 -Wl,--no-as-needed -lmkl_intel_lp64 -lmkl_lapack95_lp64 -lmkl_sequential -lmkl_core -lgfortran -fopenmp -lpthread -lm -ldl" --with-mumps=BUILD --with-metis=BUILD 
--> new with Pardiso solver
 ../configure coin_skip_warn_cxxflags=yes --prefix=/opt/casadi-linux-matlab/ipopt-install --disable-shared CXX=g++ CC=gcc F77=gfortran ADD_FFLAGS="-fPIC -fopenmp -fexceptions" ADD_CFLAGS="-fPIC -fopenmp -fno-common -fexceptions" ADD_CXXFLAGS="-fPIC -fopenmp -fno-common -fexceptions" --with-blas-lib="-L/opt/intel/mkl/lib/intel64/ -Wl,--no-as-needed -lmkl_intel_lp64 -lmkl_lapack95_lp64 -lmkl_sequential -lmkl_core -lgfortran -fopenmp -lpthread -lm -ldl" --with-lapack-lib="-L/opt/intel/mkl/lib/intel64 -Wl,--no-as-needed -lmkl_intel_lp64 -lmkl_lapack95_lp64 -lmkl_sequential -lmkl_core -lgfortran -fopenmp -lpthread -lm -ldl" --with-mumps=BUILD --with-metis=BUILD --with-pardiso="/opt/pardiso-lib/libpardiso600-GNU720-X86-64.so -lgfortran -lquadmath"
 
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
export CLANG=/usr/lib/llvm-3.8/
```

* Compile CasAdi 

```
# Clone the newest update from Develop branch - CasAdi 3.5
git clone https://github.com/casadi/casadi.git -b develop casadi
cd casadi
mkdir build && cd build
cmake -DCMAKE_INSTALL_PREFIX=/opt/casadi-linux-matlab/casadi-install -DWITH_THREAD=ON -DWITH_COMMON=ON -DINSTALL_INTERNAL_HEADERS=ON -DWITH_IPOPT=ON -DWITH_OPENMP=ON -DWITH_SELFCONTAINED=ON -DWITH_DEEPBIND=ON -DWITH_MATLAB=ON ..
## with CLANG
cmake -DCMAKE_INSTALL_PREFIX=/opt/casadi-linux-matlab/casadi-install -DWITH_THREAD=ON -DWITH_COMMON=ON -DINSTALL_INTERNAL_HEADERS=ON -DWITH_IPOPT=ON -DWITH_OPENMP=ON -DWITH_SELFCONTAINED=ON -DWITH_DEEPBIND=ON -DWITH_MATLAB=ON -DWITH_CLANG=ON ..
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
cd /opt/coin/hsl/lib
sudo ln -s libcoinhsl.so libhsl.so
```
* Export PATHs before using CasAdi
```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/casadi-linux-matlab/casadi-install/casadi
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/casadi-linux-matlab/ipopt-install/lib
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/coinhsl/lib
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/intel/mkl/lib/intel64
export LD_PRELOAD=/opt/intel/mkl/lib/intel64/libmkl_def.so:/opt/intel/mkl/lib/intel64/libmkl_avx2.so:/opt/intel/mkl/lib/intel64/libmkl_core.so:/opt/intel/mkl/lib/intel64/libmkl_intel_lp64.so:/opt/intel/mkl/lib/intel64/libmkl_intel_thread.so:/opt/intel/lib/intel64_lin/libiomp5.so
```
# Windows 64bit - MSYS2
* IPOPT
```
../configure --prefix=/d/casadi-windows-matlab/ipopt-install --enable-dependency-linking --disable-shared ADD_FFLAGS="-fPIC -fopenmp" ADD_CFLAGS="-fPIC -fopenmp" ADD_CXXFLAGS="-fPIC -fopenmp" --with-blas=BUILD --with-lapack=BUILD --with-mumps=BUILD --with-metis=BUILD --with-hsl-lib="/d/coinhsl-lib/lib" --with-hsl-incdir="/d/coinhsl-lib/include"
#NEW
../configure --prefix=/d/casadi-windows-matlab/ipopt-install --enable-dependency-linking --disable-shared ADD_FFLAGS="-fPIC -fopenmp" ADD_CFLAGS="-fPIC -fopenmp" ADD_CXXFLAGS="-fPIC -fopenmp" --with-mumps=BUILD --with-metis=BUILD -with-hsl-lib="/d/coin-hsl-lib/lib" --with-blas-lib="-L${MKL_ROOT} -Wl,--no-as-needed -lmkl_intel_lp64 -lmkl_lapack95_lp64 -lmkl_sequential -lmkl_core -lgfortran -fopenmp -lpthread -lm -ldl" --with-lapack-lib="-L${MKL_ROOT} -Wl,--no-as-needed -lmkl_intel_lp64 -lmkl_lapack95_lp64 -lmkl_sequential -lmkl_core -lgfortran -fopenmp -lpthread -lm -ldl" --with-mumps=BUILD --with-metis=BUILD --with-pardiso="/d/casadi-windows-matlab/pardiso-lib/libpardiso600-WIN-X86-64-MINGW.dll -lgfortran -lquadmath"
```
* CasAdi 
```
cmake -DCMAKE_TOOLCHAIN_FILE="/d/casadi-installation-files/toolchain-casadi.cmake" -DCMAKE_INSTALL_PREFIX="/d/casadi-windows-matlab/casadi-install" -DWITH_THREAD=ON -DWITH_COMMON=ON -DINSTALL_INTERNAL_HEADERS=ON -DWITH_IPOPT=ON -DIPOPT_LIBRARIES="/d/casadi-windows-matlab/ipopt-install/lib" -DIPOPT_INCLUDE_DIRS="/d/casadi-windows-matlab/ipopt-install/include" -DWITH_OPENMP=ON -DWITH_SELFCONTAINED=ON -DWITH_DEEPBIND=ON -DWITH_MATLAB=ON -DMATLAB_ROOT="/c/Program Files/MATLAB/R2018b" -DSWIG_EXECUTABLE="/d/casadi-windows-matlab/swig-install/bin" -DSWIG_DIR="/d/casadi-windows-matlab/swig-install" -Wno-dev -G "MSYS Makefiles" ..
```
* SWIG
```
git clone -b matlab-customdoc https://github.com/jaeandersson/swig.git
./autogen.sh
./configure --prefix=/d/casadi-windows-matlab/swig_install --host=x86_64 CC=x86_64-w64-mingw32-gcc CXX=x86_64-w64-mingw32-g++
```
