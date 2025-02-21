# 适用于 IPQ6000 设备的 OpenWrt 源码仓库
  本项目fork自https://github.com/sdf8057/ipq6000

## 注意

1. **不要用 root 用户进行编译**,如果非要使用root用户，请先设置环境变量export FORCE_UNSAFE_CONFIGURE=1
2. 国内用户编译前最好准备好梯子
3. 默认登陆IP 192.168.1.1 密码 password

## 编译命令

1. 首先装好 Linux 系统， 测试 Ubuntu 22.04.3 LTS 通过

2. 安装编译依赖

   ```bash
   sudo apt update -y
   sudo apt full-upgrade -y
   sudo apt install -y ack antlr3 asciidoc autoconf automake autopoint binutils bison build-essential \
   bzip2 ccache cmake cpio curl device-tree-compiler fastjar flex gawk gettext gcc-multilib g++-multilib \
   git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libglib2.0-dev libgmp3-dev libltdl-dev \
   libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libreadline-dev libssl-dev libtool lrzsz \
   mkisofs msmtp nano ninja-build p7zip p7zip-full patch pkgconf python2.7 python3 python3-pip libpython3-dev qemu-utils \
   rsync scons squashfs-tools subversion swig texinfo uglifyjs upx-ucl unzip vim wget xmlto xxd zlib1g-dev
   ```

3. 下载源代码，更新 feeds 并选择配置

   ```bash
   git clone -b master --single-branch https://github.com/sdf8057/ipq6000.git
   cd ipq6000
   ./scripts/feeds update -a && ./scripts/feeds install -a
   make menuconfig
   ```

4. 下载 dl 库，编译固件
（-j 后面是线程数，为便于排除错误推荐用单线程）

   ```bash
   make download -j8
   make -j1 V=s

#这时候如果报错bash: po2lmo: command not found，八成是base没有先编译出来

#可以运行以下命令先编译base，然后重新执行make -j1 V=s

make package/feeds/luci/luci-base/compile V=s

5. 二次编译：

   ```bash
   cd ipq6000
   git fetch && git reset --hard origin/master
   ./scripts/feeds update -a && ./scripts/feeds install -a
   make defconfig
   make V=s -j$(nproc)
   ```

6. 如果需要重新配置：

   ```bash
   rm -rf .config
   make menuconfig
   make V=s -j$(nproc)
   ```

7. 编译完成后输出路径：bin/targets
