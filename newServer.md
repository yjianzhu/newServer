# 1.新服务器 环境以及软件安装 
new Server environment and software installation
### 1.1 ubuntu 20.04 U盘 安装介质
[Ubuntu 官网](https://ubuntu.com/download/desktop)
[rufus U盘制作软件](https://rufus.ie/zh/)

### 1.2 启动电脑按 Del 进入 Bios, 修改为U盘启动

### 1.3 默认用户名密码 dai dai123
需要联系sam 设置静态ip地址。
##### 1.3.1 设置静态ip地址以及DNS等
### 1.4 修改ubuntu源
##### 1.4.1 从GUI界面修改
可以在设置-Ubuntu软件中修改为cuhk 或者ustc等其他源，默认的香港源网速很慢
##### 1.4.2 从CLI界面修改

    sudo sed -i 's/hk.archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list

### 1.5 开启ssh 以及设置ssh开机启动

    sudo apt install openssh-server 
    sudo systemctl enable ssh

# 2.添加新用户
### 2.1 利用adduser 添加新用户 usermod 添加管理员权限

    sudo adduser newusername
    sudo usermod -aG sudo newusername
上面的newusername 改成你想命名的用户名

### 2.2 修改hostname 用户名 以及密码

    sudo hostnamectl set-hostname newname

# 3.安装必要的环境以及软件

### 3.1 安装预编译lammps以及使用

    sudo add-apt-repository ppa:gladky-anton/lammps
    sudo add-apt-repository ppa:openkim/latest
    sudo apt-get update
    sudo apt-get install lammps-stable

依次使用上面的命令。(使用apt安装的软件可以使用apt remove 卸载。)
[lammps documentation](https://docs.lammps.org/)
__使用方法:__

    lmp_stable -in in.file
    mpirun -np number_of_threads lmp_stable -in in.file

默认使用的是openmpi的mpirun，如果发现有bug，或者是使用`mpirun -np 1`会把所有任务分配到少数cpu核心上。可以安装mpich的多线程，`sudo apt install mpich`。再修改软链接将mpirun链接到mpich上即可,`sudo ln -s file new_link`。
### 3.1.2 安装编译版lammps
自编译版本lammps可以自选安装某些包，如果上述预编译不满足需求，按以下步骤。
下载压缩包或者直接使用git克隆lammps在github上的repository。

    sudo apt install git
    git clone -b stable https://github.com/lammps/lammps.git
    cd lammps
    mkdir build
    cmake [option]../cmake
    cmake --build .
    make install
cmake option 从官网查看具体的编译选项。

安装快速傅里叶包fftw

    sudo apt install libfftw3-dev

### 3.2 安装显卡驱动以及cuda

__[cuda download guide](https://developer.nvidia.com/cuda-downloads)__
按照官网的安装后，需要修改环境变量，将cuda目录加到path里。

    sudo vim /etc/profile
将

    export PATH=/usr/local/cuda-11.4/bin${PATH:+:${PATH}}
    export LD_LIBRARY_PATH=/usr/local/cuda-11.4/lib64\${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
这两行添加到文件末尾。保存退出后
`source /etc/profile`
完成上述操作，使用`nvcc -V`查看cuda版本号。

### 3.3 安装gmx以及添加环境变量
GROMACS 官方只提供源码，需要使用cmake自行编译。apt包管理的gromacs版本过旧且无法使用gpu加速。因此按照以下命令逐行输入：
__安装cmake 3.x__

    sudo apt install cmake

在[gromacs官网](https://manual.gromacs.org/)下载gromacs（如gromacs2021.3)

    wget https://ftp.gromacs.org/gromacs/gromacs-2021.3.tar.gz
__安装gromacs__

    tar xfz gromacs-2021.3.tar.gz

    cd gromacs-2021

    mkdir build

    cd build

    cmake .. -DGMX_BUILD_OWN_FFTW=ON -DGMX_GPU=CUDA -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-11.4 \
    -DCMAKE_INSTALL_PREFIX=/usr/local/gmx2021  

    make

    make check
    
    sudo make install

__修改环境变量__
在/etc/profile末尾添加：`source /usr/local/gmx2021/bin/GMXRC`
在命令行执行`source /etc/profile`
每个用户都要自己执行一次 `source /etc/profile`才可以正确配置gromacs的环境变量

卸载 在gromacs-2021/build/ 目录 `make uninstall`

# 4 连接服务器

### 4.1 使用公私钥对登录服务器

    ssh-keygen 
一路enter下来，会生成独一无二的密码对。其中id_rsa.pub是公钥文件，id_rsa是私钥文件。可以将公钥发给服务器管理员，或者自己手动添加到`~/.ssh/authorized_keys`文件末尾。

    sudo cat id_rsa.pub >>authorized_keys
基于公私钥登录的服务器更安全。

### 4.2 使用虚拟显示器
重启后如不连接显示器，默认是无法使用anydesk等远程桌面软件。
建立虚拟显示器可以解决此问题。

    sudo apt install xserver-xorg-video-dummy
    sudo vim /usr/share/X11/xorg.conf.d/xorg.conf
把下面内容粘贴进去

    Section "Device"
        Identifier "DummyDevice"
        Driver "dummy"
        VideoRam 256000
    EndSection

    Section "Screen"
        Identifier "DummyScreen"
        Device "DummyDevice"
        Monitor "DummyMonitor"
        DefaultDepth 24
        SubSection "Display"
            Depth 24
            Modes "1920x1080_60.0"
        EndSubSection
    EndSection

    Section "Monitor"
        Identifier "DummyMonitor"
        HorizSync 30-70
        VertRefresh 50-75
        ModeLine "1920x1080" 148.50 1920 2448 2492 2640 1080 1084 1089 1125 +Hsync +Vsync
    EndSection
重启机器`sudo reboot`
