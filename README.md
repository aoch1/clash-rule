# clash

自己玩的一些日服游戏的分流list

参考了这位的教程：https://yattazen.com/tutorial/clash-custom-config.html

## 感谢大佬

ACL4SSR库：https://github.com/ACL4SSR/ACL4SSR/tree/master/Clash

blackmatrix7库：https://github.com/blackmatrix7/ios_rule_script/tree/master/rule/Clash

## 订阅链接格式

    https://dyzh.aoch1zaq.workers.dev/sub?target=clash&new_name=true&url=订阅%7C订阅&config=https%3a%2f%2fraw.githubusercontent.com%2faoch1%2fclash-rule%2frefs%2fheads%2fmain%2fpowerbyacl4ssr.ini&include=&exclude=&emoji=true&list=false&sort=false&udp=true&scv=false&append_type=false&fdn=true

### 管理页面
https://dash.cloudflare.com

## 一些订阅转换服务前缀
api.dler.io  
api.wcc.best

# 强烈建议用老mini固件
## 严格按步骤来操作，这是先辈的血泪教训
1. 当然是刷入老mini固件  
2. 在`系统-软件包-配置`的最上面的框框中注释掉最后一行代码，也即使之变为`# option check_signature`字样，然后点击提交  
3. 紧接步骤2，继续把第二个框框（发行版软件源）的内容全部删掉，替换为下面的代码，然后点击提交

       src/gz openwrt_core https://mirrors.tencent.com/lede/releases/24.10.1/targets/qualcommax/ipq807x/packages
       src/gz openwrt_base https://mirrors.tencent.com/lede/releases/24.10.1/packages/aarch64_cortex-a53/base
       src/gz openwrt_luci https://mirrors.tencent.com/lede/releases/24.10.1/packages/aarch64_cortex-a53/luci
       src/gz openwrt_packages https://mirrors.tencent.com/lede/releases/24.10.1/packages/aarch64_cortex-a53/packages
       src/gz openwrt_routing https://mirrors.tencent.com/lede/releases/24.10.1/packages/aarch64_cortex-a53/routing
       src/gz openwrt_telephony https://mirrors.tencent.com/lede/releases/24.10.1/packages/aarch64_cortex-a53/telephony

6. 返回`系统-软件包-动作`，点击`刷新列表`  
7. 紧接步骤4，在`过滤器`后面的搜索框中输入`libgcc1`并点击后面的`查找软件包`，然后点击下面的`可用软件包`再点击安装或更新。  
8. 紧接步骤5，按照同样的方法安装或更新`libpthread`和`librt`  
9. 在`系统-文件传输-上传`点击`浏览`选择从`https://github.com/vernesong/OpenClash`中下载的最新版*All.ipk。点击`上传`  
10. 紧接步骤7，在下面的上传文件列表中直接安装openclash，安装完成记得删掉上传的安装包  
11. 退出路由器管理页面然后重新登入，有条件的话直接重启路由器更好  
12. 刚刚安装的新版openclash就可以使用了

## 其他问题
### 没有中文
在过滤框中输入「luci-i18n-base-zh-cn」进行查找，接着选择「Install」安装语言包。
### open clash内核无法更新
使用WinSCP上传到对应目录
### 好看的主题
Others文件夹里有备份的Design主题离线安装包
### 沟槽的immortalwrt默认用的竟然是apk包管理（会用不了一点）
~~火速切换到opkg~~
~~算了还是用lede吧~~
呵呵，还是用老固件吧，新船载不了老AX6

# 编译参考
lede源码 https://github.com/coolsnowwolf/lede  
lede最后保留ax6的源码 https://github.com/aoch1/lede  
★说是带满血NSS驱动的源码 https://github.com/VIKINGYFY/immortalwrt/tree/main  
适用于 IPQ系列设备的 OpenWrt 源码仓库 https://github.com/LiBwrt/openwrt-6.x?tab=readme-ov-file  
各种插件和依赖仓库 https://github.com/kenzok8/small  
补充仓库 https://github.com/Boos4721/OpenWrt-Packages  

# 编译lede
## 编译依赖
保险起见一次安装少一点，分多次安装

    sudo apt update -y
    sudo apt full-upgrade -y
    sudo apt install -y ack antlr3 asciidoc autoconf automake autopoint binutils bison build-essential
    sudo apt install -y bzip2 ccache clang cmake cpio curl device-tree-compiler flex gawk gcc-multilib g++-multilib gettext
    sudo apt install -y genisoimage git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libfuse-dev libglib2.0-dev
    sudo apt install -y libgmp3-dev libltdl-dev libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libpython3-dev
    sudo apt install -y libreadline-dev libssl-dev libtool llvm lrzsz msmtp ninja-build p7zip p7zip-full patch pkgconf
    sudo apt install -y python3 python3-pyelftools python3-setuptools qemu-utils rsync scons squashfs-tools subversion
    sudo apt install -y swig texinfo uglifyjs upx-ucl unzip vim wget xmlto xxd zlib1g-dev

初始编译

    git clone https://github.com/coolsnowwolf/lede
    cd lede
    ./scripts/feeds update -a
    ./scripts/feeds install -a
    make menuconfig
    make download -j8
    make V=s -j1

二次编译

    cd lede
    git pull
    ./scripts/feeds update -a
    ./scripts/feeds install -a
    make defconfig
    make download -j8
    make V=s -j$(nproc)

如果需要重新配置：

    rm -rf .config
    make menuconfig
    make V=s -j$(nproc)

编译完成后输出路径：bin/targets  
路由后台密码password

# 编译immortalwrt
## 编译依赖
保险起见一次安装少一点，分多次安装

    sudo apt update -y
    sudo apt full-upgrade -y
    sudo apt install -y ack antlr3 asciidoc autoconf automake autopoint binutils bison build-essential
    sudo apt install -y bzip2 ccache clang cmake cpio curl device-tree-compiler flex gawk gcc-multilib g++-multilib gettext
    sudo apt install -y genisoimage git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libfuse-dev libglib2.0-dev
    sudo apt install -y libgmp3-dev libltdl-dev libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libpython3-dev
    sudo apt install -y libreadline-dev libssl-dev libtool llvm lrzsz msmtp ninja-build p7zip p7zip-full patch pkgconf
    sudo apt install -y python3 python3-pyelftools python3-setuptools qemu-utils rsync scons squashfs-tools subversion
    sudo apt install -y swig texinfo uglifyjs upx-ucl unzip vim wget xmlto xxd zlib1g-dev

初始编译

    git clone https://github.com/VIKINGYFY/immortalwrt
    cd immortalwrt
    ./scripts/feeds update -a
    ./scripts/feeds install -a
    make menuconfig
    make download -j8
    make V=s -j1
    
二次编译

    cd immortalwrt
    git pull
    ./scripts/feeds update -a
    ./scripts/feeds install -a
    make menuconfig
    make download -j8
    make V=s -j$(nproc)

如果需要重新配置：

    rm -rf .config
    make menuconfig
    make V=s -j$(nproc)

编译完成后输出路径：bin/targets  
无线密码12345678
