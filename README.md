# luci-app-adguardhome

高级 OpenWrt AdGuard Home LuCI 界面应用

[English README](README.EN.md)

## 功能特点

- 支持管理 AdGuard Home 的服务端口
- 在 LuCI 界面中下载/更新核心（支持自定义 URL 下载）
  - 对于 `.tar.gz` 文件，其文件结构需要与官方保持一致
  - 或者直接使用主程序二进制文件
- 使用 `upx` 压缩核心（依赖 `xz`，脚本会自动下载，如果 `opkg` 源无法连接，请在编译时包含此软件包）
- DNS 重定向方式：
  - 作为 `dnsmasq` 的上游
  - 将 53 端口重定向到 AdGuard Home
  - 用 53 端口替换 `dnsmasq`
- 自定义选项：
  - 自定义可执行文件路径（支持 `/tmp`，重启后自动重新下载）
  - 自定义配置文件路径
  - 自定义工作目录
  - 自定义运行日志路径
- GFWList 查询特定 DNS 服务器。另可参考 [luci-app-autoipsetadder](https://github.com/rufengsuixing/luci-app-autoipsetadder)
- 支持修改 AdGuard Home 服务登录密码
- 正序/倒序查看/删除/备份每 3 秒更新的运行日志 + 本地浏览器时区转换
- 支持手动修改 AdGuard Home 配置：
  - 支持 YAML 编辑器
  - 提供快速配置模板
- 系统升级时保留勾选文件
- 开机启动后等待网络连接自动重启 AdGuard Home（3 分钟超时，主要防止过滤器更新失败）
- 关机时备份勾选的工作目录中的文件（注意：IPK 更新时也会触发备份）
- 计划任务（以下为默认值，时间和参数可在计划任务中调整）：
  - 自动更新核心（谨慎使用）- 每天 `3:30`
  - 自动截断查询日志（每小时，限制为 2000 行）
  - 自动截断运行日志（每天 `3:30`，限制为 2000 行）
  - 自动更新 IPv6 主机并重启 AdGuard Home（每小时，无更新则不重启）
  - 自动更新 GFW 列表并重启 AdGuard Home（每天 `3:30`，无更新则不重启）

## 已知问题

- `db` 数据库不支持放在不支持 `mmap` 的文件系统，如 `jffs2` 和 `data-stk-oo`。请修改工作目录；若检测到 `jffs2`，本插件会自动将数据库软链接到 `/tmp`，但重启后将丢失 DNS 数据库
- 如发现大量来自 `127.0.0.1` 的 localhost 查询，问题原因可能是 DDNS 插件。如不使用 DDNS，请移除或注释 `/etc/hotplug.d/iface/95-ddns`。对于其他来自本地机器的异常查询，高级用户可使用 [kmod-plog-port](https://github.com/rufengsuixing/kmod-plog-port) 进行诊断

## 使用方法

- 下载发布版，用 `opkg` 安装
- 或在编译 OpenWrt 时，将代码克隆到软件包路径并设为 `y` 或 `m`

## 关于压缩

在 `jffs2` 压缩文件系统上使用 `upx` 压缩的内存和空间使用情况（单位：KB，使用最佳压缩）：

**文件大小**：
- 源文件：`14112` → `upx` 压缩后：`5309`
- 实际使用：`6260` → `upx` 压缩后：`5324`（差值：`936`）

**VmRSS 内存使用**：
- 不压缩：`14380` → `upx` 压缩后：`18496`（差值：`-4116`）

对于压缩文件系统，压缩效益存在但不显著。  
对于非压缩文件系统，性价比相当高。  
压缩是用 RAM 换取 ROM 空间 - 如认为值得则启用。

## 代理组合方法

 - 将 53 端口重定向到 AdGuard Home，配置 AdGuard Home [FAK-DNS](https://github.com/Leev1s/FAK-DNS) 且修改列表前两行的 DNS 为 `代理软件` 的 DNS。
 - 代理软件开启 `绕过大陆` ,即可完整体验分流与广告拦截。

## 截图

中文界面示例：  
![Screenshot_2019-12-23 newifi-d1 - 基础设置 - LuCI](https://user-images.githubusercontent.com/22387141/71361626-81d60900-25ce-11ea-91d5-ac4e35d5c41e.png)
![图片](https://user-images.githubusercontent.com/22387141/71361650-90242500-25ce-11ea-9727-9306a3da1357.png)
![Screenshot_2019-12-23 newifi-d1 - 日志 - LuCI(1)](https://user-images.githubusercontent.com/22387141/71361700-b944b580-25ce-11ea-8562-f68c28952b2b.png)
![Screenshot_2019-12-23 newifi-d1 - 手动设置 - LuCI](https://user-images.githubusercontent.com/22387141/71361704-bb0e7900-25ce-11ea-8042-6dd396607030.png)
