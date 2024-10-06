接下来均以普通用户admin进行操作
我的成功安装xorg与hyprland且可正常使用的平台
1. S410p笔记本
2. e5-2651v2+R9 270/HD7750+pve虚拟机(q35机型)
## 安装xorg与hyprland
### 安装对应驱动
#### intel显卡
```
sudo pkg install drm-kmod
# sudo pkg install xf86-video-intel # 可选
sudo sysrc kld_list+=i915kms
```
#### amd独显(暂不支持amd核显，据说要等到freebsd15.0)
```
sudo pkg install drm-kmod
# sudo pkg install xf86-video-intel # 可选
sudo sysrc kld_list+=amdgpu # 对于 HD7000 之后或 Tahiti 图形卡
sudo sysrc kld_list+=radeonkms # 对于较旧的显卡（HD7000 之前或 Tahiti 之前）
```
#### nvidia独显（对于较新的显卡）
```
sudo pkg install nvidia-driver
sudo sysrc kld_list+=nvidia-modeset
```
#### nvidia独显（对于较旧的显卡）
https://docs.freebsd.org/en/books/handbook/x11/#x-graphic-card-drivers
直接看文档吧
### 安装软件包并启用系统服务
```
sudo pw groupmod video -m username
sudo pkg install xorg dbus sddm wayland seatd hyprland xdg-desktop-portal-hyprland # hyprland本体
sudo pkg install alacritty waybar pavucontrol yazi rofi-wayland git neovim wl-clipboard # 常用工具
sudo sysrc dbus_enable="YES"
sudo sysrc sddm_enable="YES"
sudo sysrc hald_enable="YES"
sudo sysrc seatd_enable="YES"
sudo sysrc jackd_enable="YES"
sudo sysrc linux_enable="YES" # 配置linux兼容
```
运行后输入
```
sudo reboot
```
重启系统，如果运气好，可以直接进入sddm，并且sddm上面的会话会显示Hyprland，这种情况就是非常成功
### 报错1：`XDG_RUNTIME_DIR is not set`
可能会遇见报错`XDG_RUNTIME_DIR is not set`
在 shell 的 rc 文件中添加以下内容（例如 .shrc、.bashrc）：
```
export XDG_RUNTIME_DIR=/var/run/user/`id -u``
```
这个在freebsd14.1中已经解决，会自动帮你设置
###  报错2：virtulbox虚拟机开不起来hyprland与xorg
hyprland貌似解决不了
[Quick Start | Hyprland Wiki](https://wiki.hyprland.org/Getting-Started/Quick-start/)
官方说hyprland无法运行在虚拟机里面。
xorg的话
vbox虚拟机无法启动xorg，提示找不到显示器，那是因为没装显示驱动。
记得先在vbox设置把显示-显卡控制器改为VBoxSVGA
然后执行
```
pkg install virtualbox-ose-additions-6.1.50

startx # 检验是否可以开启xorg
```
### 报错3：平台是老式英特尔笔记本，还是无法开启xorg
如果还不行，运行
```
Xorg -configure
```
将生成的xorg.conf.new复制走
```
cp xorg.conf.new /usr/local/etc/X11/xorg.conf.d/xorg.conf
vi /usr/local/etc/X11/xorg.conf.d/xorg.conf
```
将里面Device部分的Driver项改成对应的模块[Chapter 5. The X Window System | FreeBSD Documentation Portal](https://docs.freebsd.org/en/books/handbook/x11/#x-graphic-card-drivers)
几个论坛的例子
[startX bad display name | The FreeBSD Forums](https://forums.freebsd.org/threads/startx-bad-display-name.8003/)[startX bad display name | The FreeBSD Forums](https://forums.freebsd.org/threads/startx-bad-display-name.8003/)
[Solved - Thinkpad E130 - HD 4000 - Failed to load module &quot;intel&quot; | The FreeBSD Forums](https://forums.freebsd.org/threads/thinkpad-e130-hd-4000-failed-to-load-module-intel.72048/)
### 报错4：平台13代英特尔+RTX 4060笔记本，无法开启xorg或wayland
测试，在bios使用核显独显混合模式，根据官方文档进行配置，无法使用wayland
在bios使用独显模式，无法开启xorg
这是硬件兼容的问题，无解，最好不要使用俩张显卡或者显卡加上独显的设备