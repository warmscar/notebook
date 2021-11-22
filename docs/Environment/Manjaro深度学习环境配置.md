## Manjaro 安装

默认全部安装到 SATA 移动硬盘，没有分区，硬盘空间装系统足够，另外用一块单独的硬盘存储数据

### 更改 pacman 国内源

```
sudo pacman-mirrors -c China -m rank
```

选择一块延迟最低或者更新时间最新的，也可以全选

刷新缓存

```
sudo pacman -Syy
```

### ArchlinuxCN 源

修改 `/etc/pacman.conf`

```
sudo nano /etc/pacman.conf
```

在最后加入

```
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

同步

```
sudo pacman -Syy
```

导入 GPU key

```
sudo pacman -S archlinuxcn-keyring
```

## ZSH 终端

安装 `zsh`

```
sudo pacman -S zsh
```

配置 `oh-my-zsh`

```
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

更换默认的 `shell` 为 `zsh`

```
chsh -s /bin/zsh
```

重启，就可以愉快的使用 `zsh` 了

```
reboot
```

自动补全插件 `zsh-autosuggestions` 

```
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

语法高亮插件 `zsh-syntax-highlighting`

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```

安装 `powerline` 字体

```
pacman -S powerline
pacman -S powerline-fonts
pacman -S powerline-vim
```

修改 `.zshrc` 如下

```
ZSH_THEME  = "agnoster"			# 主题
plugins=(   
		 git  
         zsh-autosuggestions 
         zsh-syntax-highlighting
)
```

使 `.zshrc` 生效

```
source ~/.zshrc
```

## 显卡驱动

开始菜单 > 设置 > 所有设置 > Manjaro-Settings Manager **选择最新的闭源驱动**。

如果驱动没有正确安装，可以用以下命令来安装：

```
sudo mhwd -a pci nonfree 0300
```

## 安装 CUDA 和 cuDNN

```
sudo pacman -S cuda cudnn
```

测试 CUDA 是否安装成功，cd 到 `opt/cuda/ssamples`目录下

```
cd 1_Utilities/deviceQuery
sudo make
./deviceQuery	# 返回 pass，则通过
```

查看 CUDA 版本，默认安装的是最新版的 `cuda`

```
nvcc -V 	# 当前安装的 cuda 版本为 10.2
```

## ssh 远程连接

**服务机**

先更新系统，再安装 `openssh` ，否则安装完可能无法启动

```
sudo pacman -Syu
sudo pacman -S openssh
```

启动/开机自启/重启服务

```
systemctl start sshd.service
systemctl enable sshd.service
systemctl restart sshd.service
```

**终端机**

```
ssh username@IP地址
```

## Python 环境

### 安装 Anaconda

一键安装

```
sudo pacman -S anaconda 
```

根据提示，激活 conda 环境，打开 `~/.zshrc`

```
sudo nano ~/.zshrc
```

将以下代码添加到 `.zshrc`最后一行

```
source /opt/anaconda/bin/activate root
```

使修改立即生效

```
source ~/.zshrc
```

### 修改 conda 源

打开 `~/.condarc`

```
sudo nano ~/.condarc
```

修改文件内容如下

```
channels:
  - defaults
show_channel_urls: true
channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

运行 `conda clean -i` 清除索引缓存，保证用的是清华镜像站提供的索引

### 修改 pip 源

改用国内的`pypi`源。执行该步骤前关闭`conda`的虚拟环境，可在终端中执行`conda deactivate`以退回到系统自带的Python环境。执行以下命令：

```
sudo pip install --upgrade pip
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### 创建虚拟环境并安装 Pytorch

新建一个名为`torch` 的 `Python 3.7` 的环境

```
conda create -n torch python=3.7
```

激活该环境

```
conda activate torch
```

安装常用库

```
pip install numpy scripy matplotlib pylint
```

安装 `Pytorch`

```
pip install torch torchvision
```

打开 Python 交互模式，测试 `Pytorch` 是否安装成功

```
python 
```

```
import torch 
print(torch.__version__)	# 1.5.1
print(torch.cuda.is_available())
```

结果返回 `True` 即可

### 创建虚拟环境并安装 TensorFlow

新建一个名为 `tf22` 的 `Python` 环境

```
conda create -n tf22 
```

激活该环境

```
conda activate tf22
```

安装 `tensorflow-gpu==2.2.0`

```
conda install tensorflow-gpu==2.2.0
```

打开 Python 交互模式，测试 `Tensorflow` 是否安装成功

```
python
```

```
import tensorflow as tf
print(tf.__version__)	# 2.2.0
print(tf.test.is_gpu_available())
```

结果返回 `True` 即可

### tensorflow-gpu2.3与CUDA10.2的兼容问题

> dlerror: libcudart.so.10.1: cannot open shared object file

原因是tensorflow 2.3.0 暂时不适配 CUDA 10.2，解决方法是：conda 安装 `cudatoolkit`

```
conda install cudatoolkit=10.1
```

再 `conda list `一下，发现 `cudatoolkit=10.0` 已安装到当前环境下：

![image-20200909201918676](https://i.loli.net/2021/11/22/HcPyaw4CMEdNzxr.png)

测试一下：

```
import tensorflow as tf
tf.test.is_gpu_available()
```

### Jupyter 多内核

想要在 `jupyter notebook` 或者 `jupyter lab` 中直接切换虚拟环境中的内核，而不是在多个虚拟环境中分别安装多个 `jupyter lab` 

在前边新建的 `tf22` 虚拟环境中安装 `jupyter kernel`

```
conda install ipykernel
```

```
python -m ipykernel install --name tf22 --display-name "tf22" --user
```

上面命令中第一个 `tf22` 是虚拟环境的名称，第二个`tf22` 是你想在 `jupyter` 中展示的名称

在 `torch` 虚拟环境中同样运行上面两条命令

```
conda install ipykernel
```

```
python -m ipykernel install --name torch15 --display-name "torch15" --user
```

可以看到 `jupyter lab` 中已经有了三个不同的 `kernel`

## Jupyter lab 远程访问

### 服务器端

生成配置文件

```
jupyter notebook --generate-config
```

设置密码并生成密钥

```
jupyter notebook password
```

```
Enter password:  ****
Verify password: ****
[NotebookPasswordApp] Wrote hashed password to /Users/b504/.jupyter/jupyter_notebook_config.json
```

查看密钥

```
sudo cat /Users/b504/.jupyter/jupyter_notebook_config.json
```

修改配置文件

```
nano ~/.jupyter/jupyter_notebook_config.py 
```

找到下面的代码行，分别修改为

```
c.NotebookApp.ip='*'
c.NotebookApp.password = u'sha:ce...刚才复制的那个密文'
c.NotebookApp.open_browser = False
c.NotebookApp.port =8888 									# 可随便指定一个端口
c.NotebookApp.allow_remote_access = True
c.NotebookApp.notebook_dir = '/home/b504/桌面/data' 		   # 修改默认文件路径为桌面上的 data 文件夹
```

在 `base` 环境中打开 `jupyter lab` 

### 本地端

网页输入 `http://b504-server:8888`

其中 `b504-server` 为工作站的 `ip` 地址

### 后台运行

`nohup` 命令：如果你正在运行一个进程，而且你觉得在退出帐户时该进程还不会结束，那么可以使用nohup命令

该命令可以在你 退出帐户/关闭终端 之后继续运行相应的进程。nohup就是不挂起的意思( no hang up)

```
nohup jupyter lab > /dev/null 2>&1 &
```

命令解释：

> `/dev/null` ，或称空设备，是一个特殊的设备文件，它丢弃一切写入其中的数据(但报告写入操作成功)，通常用于丢弃不需要的数据输出

> `command >out.file` 是将 `command` 的输出重定向到 `out.file` 文件，即输出内容不打印到屏幕上，而是输出到 `out.file` 文件中。`2>&1` 是将标准出错重定向到标准输出，这里的标准输出已经重定向到了 `out.file` 文件，即将标准出错也输出到 `out.file` 文件中。最后一个 `&` ， 是让该命令在后台执行。

> 试想2>1代表什么，2与>结合代表错误重定向，而1则代表错误重定向到一个文件1，而不代表标准输出；
> 换成2>&1，&与1结合就代表标准输出了，就变成错误重定向到标准输出。

只要服务器不关机，`jupyter lab` 的服务会一直处于运行状态，打开`ip:port` 随时可以使用

## Docker 安装

```bash
# Pacman 安装 Docker
sudo pacman -S docker

# 启动docker服务
sudo systemctl start docker 

# 查看docker服务的状态
sudo systemctl status docker

# 设置docker开机启动服务
sudo systemctl enable docker
```

### 解决sudo权限问题

```bash
sudo groupadd docker          #添加docker用户组
sudo gpasswd -a $XXX docker   #检测当前用户是否已经在docker用户组中，其中XXX为用户名
sudo gpasswd -a $USER docker  #将当前用户添加至docker用户组
newgrp docker                 #更新docker用户组
```

### 更换国内镜像

`sudo vim /etc/docker/daemon.json`

```json
{
    "registry-mirrors": [
        "https://0eunnb0b.mirror.aliyuncs.com",
        "https://registry.docker-cn.com",
        "http://hub-mirror.c.163.com",
        "https://docker.mirrors.ustc.edu.cn"
    ]
}
```

### Docker 安装 notebook

```bash
# 1.下载镜像
docker pull jupyter/datascience-notebook
# 2.运行
docker run -d -p 8080:8888 jupyter/datascience-notebook
# 3.查看镜像的日志
docker logs [output above]
# 4.复制token, 输入localhost:8080
```

其他参考链接

- [manjaro docker 深度学习环境 via 简书](https://www.jianshu.com/p/d67ed3f95d5e)

### 使用jupyter lab

```bash
docker run --name notebook -d -p 1234:8888 jupyter/datascience-notebook start.sh jupyter lab
```

[官方文档](https://jupyter-docker-stacks.readthedocs.io/en/latest/)

## pacman 使用说明

```
sudo pacman -S 软件名　		# 安装
sudo pacman -R 软件名　		# 删除单个软件包，保留其全部已经安装的依赖关系
sudo pacman -Rs 软件名 	 # 删除指定软件包，及其所有没有被其他已安装软件包使用的依赖关系
sudo pacman -Ss 软件名  	 # 查找软件
sudo pacman -Sc 		   # 清空并且下载新数据
sudo pacman -Syu　		  # 升级所有软件包
sudo pacman -Qs			   # 搜索已安装的包
```

