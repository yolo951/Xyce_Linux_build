# openMPI
1. 下载压缩包（https://www.open-mpi.org/），版本>=1.8.8
2. 解压
`
tar xzf tar xzf openmpi-5.0.2.tar.gz
`
3. 将解压后的目录改名为Trilinos
`
mv openmpi-5.0.2 openmpi
`
4. 在Trilinos中创建空的build文件，cd to it
`
cd openmpi
mkdir build
cd build
'
5.  配置，make and make install
'
# 不确定with-cuda是不是必须的，总之先加上吧
$HOME/openmpi/configure --prefix=/usr/local/openmpi --with-cuda=/usr/local/cuda-12.4 --with-cuda-libdir=/usr/local/cuda-12.4/lib64
make
sudo make install
'
6. 将路径加入环境变量，打开bashrc：nano ~/.bashrc, 加入下面几行, 然后保存source ~/.bashrc
'
MPI_HOME=/usr/local/openmpi
export PATH=${MPI_HOME}/bin:$PATH
export LD_LIBRARY_PATH=${MPI_HOME}/lib:$LD_LIBRARY_PATH
'
7. 测试自带的例子，验证安装成功
'
cd openmpi/examples
make
mpirun -np 4 hello_c
'

# Trilinos
0. 安装之前可能需要一些准备工作, 包括不限于
`
sudo apt-get update
sudo apt-get install gfortran
`
1. 下载压缩包（https://github.com/trilinos/Trilinos/releases/tag/trilinos-release-12-12-1），版本==12.12.1
2. 解压
`
tar xzf Trilinos-trilinos-release-12-12-1.tar.gz
`
3. 将解压后的目录改名为Trilinos
`
mv Trilinos-trilinos-release-12-12-1 Trilinos
`
4. 在Trilinos中创建空的build文件，cd to it
`
cd Trilinos
mkdir build
cd build
`
5. 创建脚本，命名为reconfigure, 
`
nano reconfigure
`
将cmake命令写入脚本, 然后按ctrl+X, Y, Enter键
``
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
``
6. 将reconfigure脚本设置为可执行，并执行
`
chmod u+x reconfigure
./reconfigure
`
（6.0 ./reconfigure时可能会遇到找不到某些库的情形，可以通过包管理器安装：
'
sudo apt-get install libblas-dev #安装BLAS
sudo apt-get install liblapack-dev #安装LAPACK
sudo apt-get install libsuitesparse-dev #安装AMD
'
（6.1. 在第二台机器上./reconfigure时遇到了问题，通过把reconfigure脚本中一些功能关掉，最后成功配置：
'
-DTPL_ENABLE_AMD=OFF
-DEpetraExt_BUILD_GRAPH_REORDERINGS=OFF
-DEpetraExt_BUILD_EXPERIMENTAL=OFF  
'
但是非常不建议这么搞，否则配置Xyce时会遇到问题
）
7. make and make install, -j4表示四个进程
`
make -j4
make -j4 install
`

# Xyce
1. 下载压缩包：https://xyce.sandia.gov/downloads/previous-releases/
2. 解压, 进入源代码目录
`
tar xzf Xyce-7.7.tar.gz
mv Xyce-7.7 Xyce
cd Xyce
`
3. 创建空的build文件，cd to it, configure, make and make install
``
mkdir build
cd build
./configure CXXFLAGS="-O3" ARCHDIR="$HOME/XyceLibs/Parallel" CPPFLAGS="-I/usr/include/suitesparse" --enable-mpi CXX=mpicxx CC=mpicc F77=mpif77 --enable-stokhos --enable-amesos2 --prefix=$HOME/XyceInstall/Parallel
make
make install

''
(3.1. 在第二台机器上./configure时遇到了问题，第一个问题是找不到configure脚本，通过下面的代码创建configure脚本
'
cd /home/v-tingdu/Xyce
./bootstrap
'
然后执行
'
$HOME/Xyce/configure CXXFLAGS="-O3" ARCHDIR="$HOME/XyceLibs/Parallel" CPPFLAGS="-I/usr/include/suitesparse" --enable-mpi CXX=mpicxx CC=mpicc F77=mpif77 --enable-stokhos --enable-amesos2 --prefix=$HOME/XyceInstall/Parallel
'
但是可能会遇到错误：
'
checking for flex... no
checking for lex... no
checking for bison... no
configure: error: "FLEX not found."
'
表明configure脚本在尝试配置Xyce时没有找到 flex 和 bison 工具，通过下面的代码安装，然后重新configure
'
sudo apt-get update  
sudo apt-get install flex bison  
'
sad又遇到了新的问题，由于在配置Trilinos时禁用了AMD库，导致配置Xyce时遇到了问题，因此通过sudo apt-get install libsuitesparse-dev安装AMD库，然后重新configure，但是因为安装Trilinos时禁用了
'
-DEpetraExt_BUILD_GRAPH_REORDERINGS=OFF
-DEpetraExt_BUILD_EXPERIMENTAL=OFF
'
导致出现了新的问题，我暂时禁用了amd支持，即在configure的时候加上--disable-amd，
在地三台机器上，遇到了新的问题，好像是找不到Trilinos的一个库，修改Trilinos的reconfigure文件，加上-DBUILD_SHARED_LIBS=ON，重新配置，Xyce就可以重新配置

4. Xyce被安装在了$HOME/XyceInstall/Parallel，也就是/home/v-tingdu/XyceInstall/Parallel, 将Xyce添加到PATH环境变量, 先打开.bashrc
`
nano ~/.bashrc
`
在文件末尾加上
`
export PATH=$PATH:/home/v-tingdu/XyceInstall/Parallel/bin
`
ctrl+X, Y, Enter键保存并退出，使更改生效
`
source ~/.bashrc
`

！！！！最后下载了串行版的Xyce，不需要安装openmpi，Trilinos配置时关掉mpi_enable(参考Xyce的官方文档), Xyce配置的选项参考官方文档
$HOME/Xyce/configure CXXFLAGS=''-g'' ARCHDIR="$HOME/XyceLibs/Serial" \
CPPFLAGS="-I/usr/include/suitesparse" \
--enable-stokhos \
--enable-amesos2 \
--prefix=$HOME/XyceInstall/Serial
其中CXXFLAGS=''-g''表示开启调试
