# Painless-R-compilation-and-installation-on-Ubuntu

Compiling R involves installing tons of dependencies, though there are many documents 
on the internet instructing you how to achieve it, they are kind of outdated. Some
dependencies shown here may also be out-of-date.

Note. The Bioconductor Build System (BBS)'s R installation instructions are available at 
<https://github.com/Bioconductor/BBS/blob/devel/Doc/Prepare-Ubuntu-24.04-HOWTO.md>

# Install dependencies

## Option 1: Using package management(Recommended)

From: https://cran.r-project.org/bin/linux/ubuntu/

```sh
# update indices
sudo apt update -qq
# install two helper packages we need
sudo apt install --no-install-recommends software-properties-common dirmngr
# add the signing key (by Michael Rutter) for these repos
# To verify key, run gpg --show-keys /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc 
# Fingerprint: E298A3A825C0D65DFD57CBB651716619E084DAB9
wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | sudo tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
# add the repo from CRAN -- lsb_release adjusts to 'noble' or 'jammy' or ... as needed
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"
```

To install the dependencies, run

```
sudo apt-get update
sudo apt build-dep r-base
```

## Option 2: Manually installing the dependencies

### Mandatory packages

```
sudo apt-get install \
build-essential fort77 xorg-dev liblzma-dev libblas-dev gfortran \
gcc-multilib gobjc++ aptitude \
libreadline-dev libbz2-dev libpcre2-dev \
libcurl4 libcurl4-openssl-dev \
default-jre default-jdk openjdk-8-jdk openjdk-8-jre -y
```

or use the script from <https://github.com/waldronlab/config>:

```
git clone git@github.com:waldronlab/config
cd config
sudo ./setup.sh
```

# R installation

## Using `apt`

```
# install R itself
sudo apt install --no-install-recommends r-base
```

## Compiling from source

## Export JAVA path
R requires a Java path to find the included header. This is my personal setting, I am not sure if it will work for all systems.
```
## Export a global environment for R to find the Java path
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```

### Compiling R

### Manually with .configure and make

The code below configures R with all debugging information available and without the recommended packages. 
```
./configure CFLAGS='-g -O0' CXXFLAGS='-g -O0' --enable-R-shlib --enable-memory-profiling --without-recommended-packages
```
Compile and install R. The option '-j' specifies how many cores you want to use during compilation
```
make  -j8
make check
sudo make install
```

### With the waldronlab/config script

or use `buildr.sh` script allows for R version e.g., `4-4` or `devel`.
It will install in `~/src/svn/...`.

```
cd config
sudo ./buildr.sh devel
```

Note that this would not install the `devtools` dependencies. Click [here](#devtools-package-dependencies) to see how to install them

### Optional packages
1. Recommended packages: Go to the top-level directory of the R sources, run
```
## cd ~/src/svn/r-devel/R
./tools/rsync-recommended
```

2. For building documents:

```
sudo apt-get install texinfo texlive texlive-fonts-extra -y
```

# `devtools` package dependencies
```
sudo apt-get install build-essential libcurl4-gnutls-dev libssl-dev libxml2-dev libcurl4-openssl-dev libgit2-dev -y
```

## Keywords for search
These are the error messages you will see if your system does not meet one or more dependence requirement

1. checking whether bzip2 support suffices… configure: error: bzip2 library and headers are required

2. Either pcre >= 8.32 or pcre2 library and headers are required

3. error: --with-readline=yes (default) and headers/libs are not available

4. WARNING: you cannot build PDF versions of the R manuals

5. WARNING: neither inconsolata.sty nor zi4.sty found: PDF vignettes and package manuals will not be rendered optimally

