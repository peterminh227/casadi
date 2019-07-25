![Github All Releases](https://img.shields.io/github/downloads/casadi/casadi/total.svg)

Learn all about CasADi at the [homepage](http://casadi.org) or jump to [install instructions](http://install.casadi.org)...

CasADi's MATLAB front-end uses an extension of SWIG - the software used to generate CasADi's Python front-end. The work on extending the SWIG to support MATLAB is a collaborate effort and we refer to the swig-devel mailing list (cf. this page) for detailed discussions on how this work is progressing.

# Compile source CasAdi on Ubuntu 16.04 

* Compile IPOPT from source:

```
  sudo apt-get install bison -y
  sudo apt-get install -y binutils gcc g++ gfortran git cmake liblapack-dev ipython
  sudo apt-get install -y python-dev python-numpy python-scipy python-matplotlib libmumps-seq-dev
  sudo apt-get install -y libblas-dev liblapack-dev libxml2-dev
```
