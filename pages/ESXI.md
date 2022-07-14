- > 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [zhuanlan.zhihu.com](https://zhuanlan.zhihu.com/p/166568514)
  
  > 本文将展示使用原始ISO镜像文件安装vSphere ESXI 7.0所需的所有步骤1.下载ESXi7.0镜像并刻盘，可参考下方文章进行引导盘刻录 vSphere ESXI 7.0镜像 Rufus U盘安装盘制作(Windows) 含镜像下载 vSphere ESXI 7.0镜像…
### 本文将展示使用原始 ISO 镜像文件安装 vSphere ESXI 7.0 所需的所有步骤

1. 下载 ESXi7.0 镜像并刻盘，可参考下方文章进行引导盘刻录

[vSphere ESXI 7.0 镜像 Rufus U 盘安装盘制作 (Windows) 含镜像下载](https://link.zhihu.com/?target=https%3A//blog.qiaohewei.cc/2020/07/29/esxi7_install_disk/)

[vSphere ESXI 7.0 镜像 U 盘安装盘制作 (macOS) 含镜像下载](https://link.zhihu.com/?target=https%3A//blog.qiaohewei.cc/2020/07/30/esxi7_install_disk_macos/)

2. 设备使用 U 盘引导启动，进入引导界面，默认选择第一项开始进入系统安装流程

![](https://pic3.zhimg.com/v2-47817c11341917587372ee59fdc9bc6e_r.jpg)

3. 系统加载安装文件，等待进度条完成

![](https://pic4.zhimg.com/v2-05a145e00e86f3b7e069917cf0dd665f_r.jpg)

4. 加载 VMkerne 文件，此屏幕会显示设备的 CPU 和内存配置信息

![](https://pic1.zhimg.com/v2-5f3a6cc745f56f790774228dfd3f223c_r.jpg)

5. 所有文件加载完成后会出现欢迎语，按【Enter】开始安装 VMware ESXi 7.0

![](https://pic3.zhimg.com/v2-338b400c3518de91307c92a142a88a76_r.jpg)

6. 系统出现安装许可信息界面，选择【F11】接受 “Accept and Continue”，接受许可协议，进入下一步安装；

![](https://pic4.zhimg.com/v2-bd54fec52da7427fdf61028c469da247_r.jpg)

7. 提示选择安装 VMware ESXi 使用的存储，ESXi 支持 U 盘以及 SD 卡安装，如系统中存在多块存储设备，选择需要安装系统的磁盘；。

![](https://pic4.zhimg.com/v2-2378200e26bca8434e17033e913297f3_r.jpg)

8. 如果原服务器安装有其他 ESXi 版本，则根据需求选择安装方式。

1）升级 Esxi，保留 VMFS 数据存储

2）安装 Esxi，保留 VMFS 数据存储

3）安装 Esxi，覆盖 VMFS 数据存储 (全新安装，将清除所有磁盘数据)

选择磁盘后的确认页面，确认会覆盖磁盘上原本存在的内容，按【Enter】下一步。

![](https://pic3.zhimg.com/v2-51c1336975d8ca364fc2304653a5f55a_r.jpg)

9. 提示选择键盘类型，默认 “US Default”，按【Enter】继续。

![](https://pic4.zhimg.com/v2-f735bf95cb19fbd8c01394cb1f8467bb_r.jpg)

10. 配置管理员密码（默认用户为 root）。

[ROOT 密码要求]

默认情况下，创建密码时，必须混合使用以下四个字符类的字符：小写字母，大写字母，数字和特殊字符，例如下划线或破折号，对于 ESXi 的默认密码策略，这是一项硬性要求。

*   默认情况下，密码长度大于 7 且小于 40 个字符。
*   密码必须包含至少三个字符类的字符。（大写字符（AZ），小写字符（az），数字（0-9），特殊字符（〜！@ ＃$％^＆* _- + =`| \（）{} [] :;”'< >，。？/）
*   具有三个字符类字符的密码必须至少七个字符长。
*   具有所有四个字符类别的字符的密码必须至少七个字符长。
*   密码开头的大写字符不会计入所使用的字符类数量。
*   以密码结尾的数字不会计入所使用的字符类数量。

输入第一个密码后，按【下】键 输入确认密码。

![](https://pic4.zhimg.com/v2-ae4f00163a22968562ee11cac4df5bcf_r.jpg)

11. 确认安装，此页面会显示已经选择的磁盘信息，确认无误后，按【F11】进行系统安装。

![](https://pic2.zhimg.com/v2-971444508c77a9ddf3cdf0bc4924afc9_r.jpg)

12. 系统开始安装 ESXI7.0 跑进度条，安装时间视服务器性能决定。

![](https://pic3.zhimg.com/v2-0d275f6c758ba31afc3542d7e504be56_r.jpg)

13. 系统安装完成，保持第一项默认并按【Enter】重启设备。

1）Remove the installation media before rebooting：重新启动前请移除安装介质，然后按 Enter 以重新启动计算机。

2）Reboot the server to start using ESXI x.x.x：重新启动服务器以开始使用 ESXI。

默认第一项会自动移除挂载的 ISO 设备，如果选择了第二选项，需要你手动进行卸载设备，否则会再次从 ISO 设备启动。

![](https://pic2.zhimg.com/v2-cf8a95a7ec596d66be43f9edbc9704c1_r.jpg)

14. 服务器重启完成后，进入 VMware ESXi 7.0 控制台屏幕。

![](https://pic3.zhimg.com/v2-79ada8d8820fb372876729dc75e296be_r.jpg)

15. 按【F2】输入 root 用户密码进入系统配置页。

![](https://pic4.zhimg.com/v2-e49ab022e58243979bd29f21e3ffc5bb_r.jpg)

16. 选择 “【Configure Management Network】” 选项，配置管理网络。

![](https://pic2.zhimg.com/v2-788cfc7c6c002004820096d5d2147b35_r.jpg)

17. 如果设备有多个物理网络接口，选择 “【Network Adapters】” 选项，配置管理网口。

![](https://pic3.zhimg.com/v2-f77b6fa95af43aebbc6c53a78848b95e_r.jpg)

18. 使用键盘 “上”、“下” 键选择当管理界面的网络接口，使用空格选中，选中后接口名称前为[X]，选择后按【Enter】。

[如何确认网络端口 (如何区分网卡顺序)]

将有效网络电缆依次插入网口中，如果网卡 Status(状态) 变为 Connected(已连接)，则该网口为对应网线的接口，根据需求选择即可。

![](https://pic1.zhimg.com/v2-72e4fc60f18de29d8b576805b2fa062c_r.jpg)

19. 配置管理接口 IP 地址，请选择【IPv4 Configuration】，IPV4 配置选项。

![](https://pic2.zhimg.com/v2-236be4c09da9db6d0a5bd4c0bc2c3ae5_r.jpg)

20. 选择 “set static IPV4 address and network conf iguration:” 设置静态 IPV4 地址和网络配置，配置完成后按【Enter】。

填写规划好的 IP 地址、子网掩码、网关信息，配置需与路由在同一网段，否则将无法访问 ESXI

![](https://pic4.zhimg.com/v2-219d12997c7062a948685759aca31b7b_r.jpg)

21. 配置完成后，按【ESC】退出网络配置，按【Y】保存配置并重启网络。

![](https://pic4.zhimg.com/v2-bdb78b3421753fca5b80b7455e4800e7_r.jpg)

22. 完成 ESXI IP 配置后可以在 VMware ESXi 7.0 控制台屏幕看到所配置的网络信息。

![](https://pic2.zhimg.com/v2-b4112310f09cec5c25f637eb95778251_r.jpg)

[访问 ESXI]

根据控制台屏幕显示的管理地址使用浏览器访问即可管理 ESXI

![](https://pic1.zhimg.com/v2-a5abdf5c83d52070d855aee0f1606d60_r.jpg)![](https://pic3.zhimg.com/v2-b1965bf5b6dcfc3686ba61c7b0194f8e_r.jpg)
### 至此，ESXI7.0 安装完成，Enjoy~