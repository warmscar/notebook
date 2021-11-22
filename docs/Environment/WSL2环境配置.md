# 官方文档

[适用于 Linux 的 Windows 子系统安装指南 (Windows 10)](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10)

> 以下相关环境配置均基于 Ubuntu 2004

# 访问wsl2目录

```
\\wsl$
```

找到文件夹位置后可以直接右键：映射网络驱动器

# 查看已安装的WSL版本

```shell
wsl -l -v
```

# 更新源

[清华Ubuntu镜像站](https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/)

- 备份源文件

  ```bash
  # 源文件sources.list 在/etc/apt/目录下
  
  # 备份源文件
  sudo cp sources.list sources.list.bak 
  ```

- 替换`/etc/apt/sources.list` 文件内容

  ```bash
  # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
  deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
  # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
  deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
  # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
  deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
  # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
  deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
  # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
  
  # 预发布软件源，不建议启用
  # deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
  # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
  ```

- 更新源

  ```bash
  sudo apt-get update 
  sudo apt-get upgrade 
  ```

# 安装GCC

```bash
sudo apt install gcc
```

# 安装ZSH

**安装 `zsh`**

```
sudo apt update
sudo apt install zsh
```

**安装配置 `oh-my-zsh`**

```
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

或者

```
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

超时使用

```bash
wget https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

更换默认的 `shell` 为 `zsh`

```
chsh -s /bin/zsh
```

重启，就可以愉快的使用 `zsh` 了

**插件**

自动补全插件 `zsh-autosuggestions` 

```
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

语法高亮插件 `zsh-syntax-highlighting`

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```

安装 `powerline` 字体

> [Github - powerline字体安装说明](https://github.com/powerline/fonts)

```
sudo apt install powerline
sudo apt install fonts-powerline
```

修改 `.zshrc` 

```
sudo nano ~/.zshrc
```

找到如下代码行并修改为

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

> [zsh 主题 - powerlevel10k](https://blog.csdn.net/kk3909/article/details/105120234/)

# Vim 配置

安装vimrc

> [github - vi mrc](https://github.com/amix/vimrc)

```bash
git clone --depth=1 https://github.com/amix/vimrc.git ~/.vim_runtime
sh ~/.vim_runtime/install_awesome_vimrc.sh
```

显示行号

`vim ~/.vimrc`

将`set nu`写入配置文件

# Anaconda

从清华镜像站下载 `Anaconda` 离线安装文件

```
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2020.11-Linux-x86_64.sh
```

安装

```
sudo zsh Anaconda3-2020.11-Linux-x86_64.sh
```

根据提示安装，提示让选择默认安装路径，输入 `/opt/anaconda3`

根据提示设置默认不进入`base环境`（先根据后边的步骤配置环境变量，此时conda命令还不是系统命令）

```bash
conda config --set auto_activate_base false
```

安装完成后，重新进入 `shell` 

添加环境变量，`sudo nano ~/.zshrc`

```
export PATH="$PATH:/opt/anaconda3/bin"
```

立即生效，`source ~/.zshrc`

修改 `conda` 源为清华镜像源 

```
sudo nano ~/.condarc
```

追加以下内容到 `.condarc`

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

运行 `conda clean -i` 清除索引缓存，保证用的是镜像站提供的索引

# nodejs

去 `nodejs` 查看最新的版本号，当前最新版为 `14.5.0`

添加源后安装

nodejs 的每个大版本号都有相对应的源，比如这里的 10.x.x版本的源是

```
https://deb.nodesource.com/setup_10.x
```

终端执行

```
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
```

稍等片刻，源已经添加完毕，再执行：

```
sudo apt-get install -y nodejs
```

顺带一提，如果你要安装 `12.x.x` 的版本，只需要修改添加源地址中的数字即可，比如：

```
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```

最后验证一下，执行`nodejs -v` 即可出现刚才安装的版本号

# Java 8

> [如何在 Ubuntu 20.04 上安装 Java](https://zhuanlan.zhihu.com/p/137114682)

安装 OpenJDK 8

```
sudo apt update
sudo apt install openjdk-8-jdk
```

检查 Java 版本

```
java -version
```

`JAVA_HOME` 环境变量

首先使用 `update-alternatives`找到 Java 安装路径:

```
sudo update-alternatives --config java
```

找到安装路径如下

- OpenJDK 8 is located at `/usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java`

打开`/etc/environment`文件

```
sudo nano /etc/environment
```

在末尾添加

```
JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
```

让当前修改生效

```
source /etc/environment
```

验证 `JAVA_HOME` 环境变量被正确设置

```
echo $JAVA_HOME
```

# 配置代理

相关设置

- `v2ray` 设置 允许来自局域网的链接

- `win10 防火墙` 允许`v2ray`

临时配置

```bash
export http_proxy='http://211.64.154.142:10809'
export https_proxy='http://211.64.154.142:10809'
```

> IP为宿主机的IP地址，端口为代理服务器的端口，可以在windows的设置里查看

测试

```bash
curl google.com
```

---

以下方法`wsl2` 没啥用。。。

安装 `proxychains-ng` 代理

```bash
cd ~/Download
git clone https://github.com/rofl0r/proxychains-ng
cd proxychains-ng
./configure --prefix=/usr --sysconfdir=/etc # 编译配置，设置bin文件安装目录和config文件目录
make # 编译
sudo make install # 安装
sudo make install-config # 安装proxychains.conf配置文件到上面设定的目录
```

安装 `proxychains`

```bash
sudo apt install proxychains
```

---

参考教程

- [WSL2通过Clash for Windows使用Windows代理](https://www.cnblogs.com/sinicheveen/p/13866914.html)

- [WSL2中访问宿主机Windows的代理](https://zinglix.xyz/2020/04/18/wsl2-proxy/)

- [WSL2怎么设置才能走clash的代理](https://www.v2ex.com/t/677083)