# 配置本地和远程开发环境

# 1. 配置免密登录

在linux终端里，

    cd ~/.ssh
    ssh-keygen
生成一个 `id_rsa` 和`id_rsa.pub` 文件。分别是私钥和公钥，将其中的公钥复制到`authorize_keys`的末尾。

    cat id_rsa.pub >>authorized_keys

下载`id_rsa`文件到本地，在xshell可以指定用这个私钥登录。
# 2. 配置vscode 连接远程服务器
下载vscode，在其中安装remote ssh，remote explorer，remote development，remote tunnels插件。
然后在侧边栏找到远程资源管理器,选择远程，在ssh栏点击设置，打开config，
按照下面的格式写入配置文件。

    Host hostname
        HostName 144.214.24.119
        User yongjian
        ForwardAgent yes
        IdentityFile  "/path/to/id_rsa"

# 3. 配置conda环境

    conda create -n env_name
    conda activate env_name
    conda install jupyter-lab notebook

# 4. 配置jupyter 和vscode jupyter

使用vscode ssh连接上远程服务器，打开一个workspace，创建一个`yourfile.ipynb`。如果你配置好了conda和conda环境，则可以在其中直接使用jupyter交互界面，可以输入markdown和python代码块。和网页一样，可以查看jupyter变量。

如果你的conda环境里没有安装ipykernel, vscode 会提示你安装的。