<font size=5>

# Fundamental usage of Linux

## 0. Computer Architech
<image src='image/1.png'>

## 1. Everything is a file
<image src='image/folder.jpg'>

## 2. Command
<image src='image/command.jpg'>

### 2.1 Path and env
`env` see the system path. All packages or executable file in path can be executed directly. Others must be executed by `./` and the absolute path.such as `./home/yongjian/execute/MC_multi_chian`.

type `env` or `echo $PATH`, you'll see 
<image src='image/path.jpg'>

Every user has himself PATH, there are two methods. Modify `/etc/profile` for all user, `/home/${yourself}/.bashrc` for one user.

`source /etc/profile` or `source /home/${yourself}/.bashrc` to refresh path.
<image src='image/profile.jpg'>
 __SECURITY FIRST__

### 2.1 whereis and apt(Advanced Packaging Tool)

Most fundamental applications can be found in  `/usr/bin`, such as `python`,`mpirun`.  If you want to use parallel computing, you can type `whereis mpirun`. 
<image src='image/whereis.jpg'>
If you are curious, use `ls -l` to show the detail of mpirun file.
<image src='image/ls.jpg'>
Different color means different file types.
<image src='image/color.png' width="200%">

Use `apt` install software or depends if there is.

fftw3 is a fundamental scientific computing library, `sudo apt install fftw3` is your better choice.

__I don't think that compiling every lib or application yourself is OK__


## 3 Simulation Softwares

Lammps and Gromacs are installed in most machine, if you type `whereis lmp_stable` or `whereis lmp` or `whereis gmx` and don't find them, follow the installation on their official website.(some online tutorial maybe out of date)

## 4 File system and mount

chown chmod mount