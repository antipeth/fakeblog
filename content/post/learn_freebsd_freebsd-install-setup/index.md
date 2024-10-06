## 更新系统
先以root身份登陆，因为没有sudo，所以普通用户不能获得管理员权限，后面会安装
```
pkg update
```
在这一布可能会遇见问题，报错如下
```
admin@freebsd ~> sudo pkg install inxi
Updating FreeBSD repository catalogue...
pkg: No SRV record found for the repo 'FreeBSD'
pkg: packagesite URL error for pkg+http://pkg.FreeBSD.org/FreeBSD:13:amd64/quarterly/packagesite.pkg -- pkg+:// implies SRV mirror type
pkg: packagesite URL error for pkg+http://pkg.FreeBSD.org/FreeBSD:13:amd64/quarterly/packagesite.txz -- pkg+:// implies SRV mirror type
Unable to update repository FreeBSD
Error updating repositories!

admin@freebsd ~ [3]> pkg search inxi
pkg: Repository FreeBSD missing. 'pkg update' required
pkg: Repository FreeBSD cannot be opened. 'pkg update' required

admin@freebsd ~ [1]> sudo pkg update
Updating FreeBSD repository catalogue...
pkg: No SRV record found for the repo 'FreeBSD'
Fetching meta.conf: 100%    163 B   0.2kB/s    00:01    
pkg: packagesite URL error for pkg+http://pkg.FreeBSD.org/FreeBSD:13:amd64/quarterly/packagesite.pkg -- pkg+:// implies SRV mirror type
pkg: packagesite URL error for pkg+http://pkg.FreeBSD.org/FreeBSD:13:amd64/quarterly/packagesite.txz -- pkg+:// implies SRV mirror type
Unable to update repository FreeBSD
Error updating repositories!
```
提取报错，就是找不到pkg源的SRV记录
```
pkg: No SRV record found for the repo 'FreeBSD'
```
[problems with pkg 1.20.8 | The FreeBSD Forums](https://forums.freebsd.org/threads/problems-with-pkg-1-20-8.90655/)
解决方法如下

```sh
mkdir -p /usr/local/etc/pkg/repos
cp /etc/pkg/FreeBSD.conf /usr/local/etc/pkg/repos/
vi /usr/local/etc/pkg/repos/FreeBSD.conf
```

将：

```FreeBSD.conf
# $FreeBSD$
#
# To disable this repository, instead of modifying or removing this file,
# create a /usr/local/etc/pkg/repos/FreeBSD.conf file:
#
#   mkdir -p /usr/local/etc/pkg/repos
#   echo "FreeBSD: { enabled: no }" > /usr/local/etc/pkg/repos/FreeBSD.conf
#

FreeBSD: {
  url: "pkg+http://pkg.FreeBSD.org/${ABI}/quarterly",
  mirror_type: "srv",
  signature_type: "fingerprints",
  fingerprints: "/usr/share/keys/pkg",
  enabled: yes
}
```

改为：

```FreeBSD.conf
# $FreeBSD$
#
# To disable this repository, instead of modifying or removing this file,
# create a /usr/local/etc/pkg/repos/FreeBSD.conf file:
#
#   mkdir -p /usr/local/etc/pkg/repos
#   echo "FreeBSD: { enabled: no }" > /usr/local/etc/pkg/repos/FreeBSD.conf
#

FreeBSD: {
  url: "http://pkg.FreeBSD.org/${ABI}/quarterly",
  mirror_type: "http",
  signature_type: "fingerprints",
  fingerprints: "/usr/share/keys/pkg",
  enabled: yes
}
```

这是官方管理员给出的解决方案。
再执行更新
```
pkg update
```
安装sudo

```sh
pw groupmod wheel -m username
pkg install sudo
visudo
```
接下来弹出vi编辑器，在其中找到
```sh
## Same thing without a password
```

把下面那行的注释取消(就是下面那行的#号删掉，使用hjkl移到到那个需要删除的位置，按x，就可以删除光标所在的字符)。然后按`ESC`,再输入`:wq`保存并退出。
然后就可以使用sudo了，这是可以使用
```
exit # 注销当前用户，重新以之前创建的普通用户身份登录
```
或者
```
su admin # 切换回之前创建的普通用户admin
```