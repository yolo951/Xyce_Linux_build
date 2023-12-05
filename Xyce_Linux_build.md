# openMPI
1. 下载压缩包（https://www.open-mpi.org/），版本>=1.8.8
2. 忘了

# Trilinos
0. 安装之前可能需要一些准备工作, 包括不限于
'
sudo apt-get update
sudo apt-get install gfortran
'
1. 下载压缩包（https://github.com/trilinos/Trilinos/releases/tag/trilinos-release-12-12-1），版本==12.12.1
2. 解压
'
tar xzf Trilinos-trilinos-release-12-12-1.tar.gz
'
3. 将解压后的目录改名为Trilinos
'
mv Trilinos-trilinos-release-12-12-1 Trilinos
'
4. 在Trilinos中创建空的build文件，cd to it
'
cd Trilinos
mkdir build
cd build
'
5. 创建脚本，命名为reconfigure, 
'
nano reconfigure
'
将cmake命令写入脚本, 然后按ctrl+X, Y, Enter键
''
#!/bin/sh
SRCDIR=$HOME/Trilinos
ARCHDIR=$HOME/XyceLibs/Parallel
FLAGS="-O3 -fPIC"

cmake \
-G "Unix Makefiles" \
-DCMAKE_C_COMPILER=mpicc \
-DCMAKE_CXX_COMPILER=mpic++ \
-DCMAKE_Fortran_COMPILER=mpif77 \
-DCMAKE_CXX_FLAGS="$FLAGS" \
-DCMAKE_C_FLAGS="$FLAGS" \
-DCMAKE_Fortran_FLAGS="$FLAGS" \
-DCMAKE_INSTALL_PREFIX=$ARCHDIR \
-DCMAKE_MAKE_PROGRAM="make" \
-DTrilinos_ENABLE_NOX=ON \
-DNOX_ENABLE_LOCA=ON \
-DTrilinos_ENABLE_EpetraExt=ON \
-DEpetraExt_BUILD_BTF=ON \
-DEpetraExt_BUILD_EXPERIMENTAL=ON \
-DEpetraExt_BUILD_GRAPH_REORDERINGS=ON \
-DTrilinos_ENABLE_TrilinosCouplings=ON \
-DTrilinos_ENABLE_Ifpack=ON \
-DTrilinos_ENABLE_Isorropia=ON \
-DTrilinos_ENABLE_AztecOO=ON \
-DTrilinos_ENABLE_Belos=ON \
-DTrilinos_ENABLE_Teuchos=ON \
-DTrilinos_ENABLE_COMPLEX_DOUBLE=ON \
-DTrilinos_ENABLE_Amesos=ON \
-DAmesos_ENABLE_KLU=ON \
-DTrilinos_ENABLE_Amesos2=ON \
-DAmesos2_ENABLE_KLU2=ON \
-DAmesos2_ENABLE_Basker=ON \
-DTrilinos_ENABLE_Sacado=ON \
-DTrilinos_ENABLE_Stokhos=ON \
-DTrilinos_ENABLE_Kokkos=ON \
-DTrilinos_ENABLE_Zoltan=ON \
-DTrilinos_ENABLE_ALL_OPTIONAL_PACKAGES=OFF \
-DTrilinos_ENABLE_CXX11=ON \
-DTPL_ENABLE_AMD=ON \
-DAMD_LIBRARY_DIRS="/usr/lib" \
-DTPL_AMD_INCLUDE_DIRS="/usr/include/suitesparse" \
-DTPL_ENABLE_BLAS=ON \
-DTPL_ENABLE_LAPACK=ON \
-DTPL_ENABLE_MPI=ON \
$SRCDIR
''
6. 将reconfigure脚本设置为可执行，并执行
'
chmod u+x reconfigure
./reconfigure
'
7. make and make install, -j4表示四个进程
'
make -j4
make -j4 install
'

# Xyce
1. 下载压缩包
2. 解压, 进入源代码目录
'
tar xzf Xyce-7.7.tar.gz
mv Xyce-7.7 Xyce
cd Xyce
'
3. 创建空的build文件，cd to it, configure, make and make install
''
mkdir build
cd build
./configure CXXFLAGS="-O3" ARCHDIR="$HOME/XyceLibs/Parallel" CPPFLAGS="-I/usr/include/suitesparse" --enable-mpi CXX=mpicxx CC=mpicc F77=mpif77 --enable-stokhos --enable-amesos2 --prefix=$HOME/XyceInstall/Parallel
make
make install
''
4. Xyce被安装在了$HOME/XyceInstall/Parallel，也就是/home/v-tingdu/XyceInstall/Parallel, 将Xyce添加到PATH环境变量, 先打开.bashrc
'
nano ~/.bashrc
'
在文件末尾加上
'
export PATH=$PATH:/home/v-tingdu/XyceInstall/Parallel/bin
'
ctrl+X, Y, Enter键保存并退出，使更改生效
'
source ~/.bashrc
'