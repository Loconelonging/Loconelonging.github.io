# pip

*   pip安装和配置
    *   获取
    *   升级
    *   配置国内镜像源
*   pip核心功能
    *   包安装与管理
    *   依赖管理
        *   pip freeze
        *   pip reqs
*   pip高级技巧
    *   选择性安装
    *   缓存管理
        *   查看缓存位置
        *   设置缓存位置
    *   构建与安装本地包
    *   查询包的历史版本
    *   在代码中安装
*   pip常见疑难
    *   常见错误处理
    *   依赖解析策略
    *   性能优化
*   pip生态系统
    *   相关工具
    *   与虚拟环境集成

### 获取pip

```bash
python -m pip --version
# 或
pip --version
```

linux/macos

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
```

windows

    (Invoke-WebRequest https://bootstrap.pypa.io/get-pip.py).Content | python -

### 升级pip

    python -m pip install --upgrade pip

### 换源

临时

    pip install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package

永久

    pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

国内常用源：

    清华大学源 https://pypi.tuna.tsinghua.edu.cn/simple
    豆瓣源 https://pypi.douban.com/simple/
    中国科学技术大学 https://pypi.mirrors.ustc.edu.cn/simple
    阿里云 http://mirrors.aliyun.com/pypi/simple/
    中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/

***

### 包安装

    基础安装
    pip install requests

    指定版本
    pip install django==4.2
    pip install "flask>=2.0,<3.0"

    升级
    pip install --upgrade pandas

    卸载
    pip uninstall numpy

### 依赖管理

*   使用虚拟环境
*   使用uv等现代化管理工具，开发时就将依赖写入

pip freeze：

把当前环境下所有pip安装的包都导出到requirements.txt中，可能引入不需要的包。不如pipreqs

    # 生成包含所有已安装包的 requirements.txt
    pip freeze > requirements.txt

    # 如果只想导出特定包（比如只导出项目用到的），可以用 pipreqs（需要先安装）
    pip install pipreqs

    # 生成当前目录下项目实际依赖的包（更精准）
    pipreqs . --encoding=utf8 --force

pipreqs：

    安装
    pip install pipreqs
    （windows系统可能报编码错误，可指定编码格式解决：
    pipreqs . --encoding=utf8 --force）

    定位到项目根目录路径再执行：
    pipreqs ./

    最终，requriements.txt将被生成到./路径下

    从文件安装
    pip install -r requirements.txt

    高级依赖解析：
    pip install --no-deps package-name  # 仅安装指定包，不安装依赖
    pip install --pre package-name     # 包含预发布版本

检查已安装包的依赖关系：

    # 查看单个包的依赖详情
    pip show <package-name>

    # 示例：查看 requests 包的依赖
    pip show requests

    # 检查所有已安装包的依赖冲突（需要安装 pip-check-reqs）
    pip install pip-check-reqs

    # 检查未使用的依赖
    pip-check-reqs

    # 检查缺失的依赖
    pip-check-reqs --requirements requirements.txt

*   `pip show` 会显示包的详细信息，其中 `Requires` 字段就是该包的依赖项，Required-by 是依赖该包的其他包
*   `pip-check-reqs` 是第三方工具，能帮你发现：
    *   代码中用到但未在 `requirements.txt` 中声明的包（缺失依赖）
    *   `requirements.txt` 中声明但代码中未使用的包（冗余依赖）

pipdeptree:（进阶）可视化依赖树

    # 安装依赖树工具
    pip install pipdeptree

    # 查看所有包的依赖树
    pipdeptree

    # 查看特定包的依赖（比如 requests）
    pipdeptree -p requests

    # 检查循环依赖或冲突
    pipdeptree -c

*   生成依赖文件：用 pip freeze > requirements.txt（全量）或 pipreqs .（精准）
*   检查依赖状态：pip check 快速检查冲突，pip show <包名> 查看单个包依赖
*   可视化依赖：安装 pipdeptree 后用 pipdeptree 查看完整依赖树，排查复杂冲突

### 环境管理

查看已安装包：

    pip list # 查询已经安装的所有包
    pip list | findStr requests # 查询包含requests的包

查看包详情

    pip show requests

查看过时包

    pip list --outdated

***

### 安装可选依赖

（某些包，官网installation中可看到提示
pip install -U "somepack\[gui,rag,code\_interpreter,mcp]"）

    pip install "package[extra1,extra2]"
    # 例如
    pip install "requests[security]"

### 平台特定依赖

    pip install --platform win_amd64 package-name  # 64位Windows
    pip install --platform win32 package-name     # 32位Windows
    # 强制安装Linux版本的包（适用于WSL或特殊场景）
    pip install --platform manylinux2014_x86_64 --only-binary=:all: package-name
    # 安装macOS版本的包（通常不推荐，仅用于特殊测试）
    pip install --platform macosx_10_15_x86_64 --only-binary=:all: package-name

通常不需要手动，pip可自行选择。主要用于：为其它平台预先下载依赖；解决某些包的兼容性问题；企业内网部署时预先缓存依赖。

***

### 查看缓存位置

    pip cache dir
    # 清理特定包的缓存
    pip cache remove package-name

shiyong有的缓存位置可能会被自定义配置覆盖，可以检查pip全局配置

    pip config list

### 设置缓存位置

    pip config set global.cache-dir "D:\data\pip\cache"

### 查看缓存

    pip cache list

### 清理缓存

    pip cache purge

### 禁用缓存

    pip install --no-cache-dir package-name

### 从本地安装

    pip install /path/to/package

### 从源码安装（可编辑模式）

    pip install -e /path/to/package  # 适合开发模式

### 构建分发包

    pip install build
    python -m build

### 查询包的历史版本

    pip index versions xxxx

### 在代码中安装

pip库安装：(可能已经失效)

    import pip

    def install_package(package_name):
        try:
            pip.main(['install', package_name, '-i', 'https://pypi.douban.com/simple/'])
            print(f"Successfully installed {package_name}")
        except Exception as e:
            print(f"Failed to install {package_name}: {str(e)}")

    # 示例：安装requests库
    install_package('requests')

subprocess安装：(调用系统的pip命令来安装)

    import subprocess

    def install_package(package_name):
        try:
            subprocess.check_call(['pip', 'install', package_name])
            print(f"Successfully installed {package_name}")
        except subprocess.CalledProcessError:
            print(f"Failed to install {package_name}")

    # 示例：安装requests库
    install_package('requests')

***

### 常见错误处理

权限问题：
`pip install --user package-name  # 用户级安装
`
版本冲突
`pip install --ignore-installed package-name
`
ssl错误
`pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org package-name
`

### 依赖解析策略

旧解析器（2020年前）：简单但可能不一致
新（默认）：更严格但更可靠

强制使用旧解析器：
`pip install --use-deprecated=legacy-resolver package-name`

### 性能优化

并行安装

    pip install -U pip  # 确保pip最新版
    pip install --use-feature=fast-deps package-name

二进制缓存
`pip install --cache-dir /path/to/cache package-name
`

***

### pip生态系统

#### 相关工具：

pip-tools:（高级依赖管理）

    pip install pip-tools
    pip-compile requirements.in  # 生成精确的requirements.txt

pipdeptree:（依赖树可视化）

    pip install pipdeptree
    pipdeptree

pipx:(全局应用安装)

> pipx 的核心逻辑：
> 1.为每个工具创建独立的虚拟环境（默认路径：~/.local/pipx/venvs/）；
> 2.将工具的可执行文件（如 black 命令）链接到系统的 PATH 目录（如 ~/.local/bin/）；
> 3.你在终端任意位置输入 black 就能直接使用，无需激活虚拟环境。

    pip install pipx
    pipx install black

#### 与虚拟环境集成：

venv(python内置):

    python -m venv myenv
    source myenv/bin/activate  # Linux/macOS
    myenv\Scripts\activate     # Windows
    pip install package-name

virtualenv（更强大）:

    pip install virtualenv
    virtualenv myenv

***

***

# conda

*   什么是conda
    *   conda与anaconda区别
    *   conda与pip区别
*   下载安装
*   配置与使用
    *   配置下载源
    *   环境管理
        *   创建/删除 环境
        *   激活/切换 环境
        *   下载/卸载 库
        *   导入/导出 环境
    *   试运行py文件

## 什么是conda

是一个可以在多操作系统上运行的 **包和环境管理系统**

利用 conda 使得不同版本Python环境、不同版本模块 共存和灵活切换。

### conda 与 anaconda 区别

诸如 Anaconda、Miniconda、Bioconda（用于计算生物学）等都是**基于 conda 的工具软件**，这些软件均包含 conda 包和环境管理器
**Anaconda**是一个大而全的软件发行版，是一个预先建立和配置好的模块集，能够安装在操作系统上使用。它包含了Python本身和数百个第三方开源项目的二进制文件，如 numpy、scipy、ipython、matplotlib等，这些库基本是为了方便处理数据科学相关的问题。
**Miniconda**也是一个软件发行版，但它仅包含python、conda 和 conda 的依赖项，本质上就是一个空的用来安装 conda 环境的安装器，它没有 Anaconda 中那么多的包，可以理解为 Anaconda 的精简版，能够方便用户按照自己的需求，从零开始构建任意的环境。

### Conda 与 pip 的区别

pip，它是 Python 包的通用管理器，全称是 Pip Install Packages，它是一个Python官方认证的包管理工具，只能管理python包而无法安装非Python依赖项，例如HDF5、MKL、LLVM等，通常用于在相互独立的环境中安装发布在 Python Package Index（PyPI）上面的包。Pip和 PyPI 均由Python Packaging Authority（PyPA）管理和支持。

而 conda 既具有 pip 的包管理能力，同时也具有 vitualenv 的环境管理功能，因此在相互独立的环境中，可以简单认为 conda 就是 pip 和 vitualenv 的组合，在包管理这方面，conda 不仅能管理 python 包，还可以管理任何类型的、用任何语言写的包和依赖，包来源是 Anaconda repo（默认）和 Cloud。

简单而言，pip 与 conda 最关键的区别在于，在使用 pip 之前，必须通过系统软件包管理器下载和安装python解释器，而 conda 可以直接安装 python 软件包以及解释器，但 conda 只能在 conda 环境下安装各类的包，因此需要先创建 conda 环境。

## 下载安装

软件包的下载来源有两种：
官方网站：<https://docs.anaconda.com/free/miniconda/miniconda-install/>

清华源：<https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/>
（镜像源关闭了，好像是因为未取得 Anaconda 和 Miniconda 的授权。）

*   检查系统条件是否满足
*   下载相应安装包 \`Miniconda3-latest-Windows-x86\_64.exe
*   执行安装
*   检查安装成功  `conda --version` `conda list`

conda分为anaconda和miniconda，官网为：
miniconda 官网：<https://conda.io/miniconda.html>
anaconda 官网：<https://www.anaconda.com/download>

## 配置并使用conda

### 配置下载源

```
conda config --set show_channel_urls yes
(使得在查看conda软件包的下载链接时，显示我们手动配置的通道地址。可以不用)

conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/cloud/bioconda/
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/main/

下面这个我没用过, 可以添加一下试试看.
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/r/


目前国内提供conda镜像的大学
  清华大学: https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/
  北京外国语大学: https://mirrors.bfsu.edu.cn/help/anaconda/
  南京邮电大学: https://mirrors.njupt.edu.cn/
  南京大学: http://mirrors.nju.edu.cn/
  重庆邮电大学: http://mirror.cqupt.edu.cn/
  上海交通大学: https://mirror.sjtu.edu.cn/
  哈尔滨工业大学: http://mirrors.hit.edu.cn/#/home
  (目测哈工大的镜像同步的是最勤最新的)


查看已经添加的channels
conda config --get channels

```

也可以直接修改配置文件`.condarc`，在`C:\Users\用户名\`下。只有执行了前面的配置下载源命令后才有该文件，默认情况没有，可以手动创建。

如果不配置下载源，会使用默认的软件包通道，即anaconda官方仓库获取。具体软件包通道情况可查看`conda config --show`.

重置下载源信息可用`conda config --remoce-key channels`

## 环境管理

### 创建/删除 环境

```
创建环境
conda create --name myenv python=3.8 numpy matplotlib

查看当前存在的环境
conda env list 

删除环境
conda remove --name myenv --all
conda env remove -n env_name

重命名环境 
conda create -n newname --clone oldname  	

```

### 激活/切换 环境

    # windows
    conda activate myenv
    conda deactivate

    # Linux/Unix
    source activate myenv

### 下载/卸载 库

```
# 下载安装
conda install scipy
有时候conda提供的不全，需要在激活的conda中pip安装。（conda 版本选择用= ，pip用 == ）

# 卸载
conda remove scipy    ？？？？（不知道用啥，再看看，两个文章两种写法。）

conda install gatk
conda install gatk=3.7					# 安装特定的版本:
conda install -n env_name gatk   		# 将 gatk 安装都 指定env_name中

#搜索
conda search xxxx
#查看安装位置
which xxx
#查看已安装的库
conda list
conda list -n env_name （env_name下的库）
$conda install gatk
conda install gatk=3.7					# 安装特定的版本:
conda install -n env_name gatk   		# 将 gatk 安装都 指定env_name中
#更新指定库
conda update gatk
conda update --all   	# 升级全部库


```

### 导入/导出 环境

    conda env export > environment.yml

    conda env create -f environment.yml

### 试运行py文件（略）

### 卸载conda

*   清理：rm -rf /opt/anaconda3
*   删除 \~/.bash\_profile中anaconda的环境变量
*   删除Anaconda的可能存在隐藏的文件
*   rm -rf ~/.condarc ~/.conda \~/.continuum
*   经过以上步骤后，Anaconda 就被彻底删除了。

### 迁移conda环境

环境打包：

    conda pack -n 虚拟环境名称 -o environment.tar.gz

    如果报错：No command ‘conda pack’
    尝试使用：conda install -c conda-forge conda-pack

复制到新的电脑环境

进入conda安装目录：/anaconda(或者miniconda)/envs/

    # 对于 ubuntu 可以通过 whereis conda 查看 conda的安装路径
    # cd 到 conda 的安装路径
    mkdir environment

    # 解压conda环境：
    tar -xzvf environment.tar.gz -C  environment

使用conda env list查看虚拟环境，进入迁移的环境内，通过 pip list 查看迁移前后 包的安装情况

## CondaHTTPError 问题

对于创建环境或者安装库的时候可能出现 CondaHTTPError 的问题，提供一下两种解决方案

1.添加国内镜像源，或者采用以下方法皆可
在系统C盘用户文件夹下面，会有一个 .condarc 的文件，在此可以手动自行添加 channels

2.可能是现有的库文件版本较低，可以尝试升级下现有的库，方法如下:

    conda update --all   	# 升级全部库

