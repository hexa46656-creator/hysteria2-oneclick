# Hysteria2 一键部署脚本

## 1. 项目用途

本项目用于在已经部署过 vpsguard 的 Ubuntu LTS 服务器上，自动化安装 Hysteria2 并完成基础服务配置。脚本会自动生成自签证书、随机认证密码、systemd 服务、客户端信息文件，并在终端输出可直接使用的客户端配置。

## 2. 适用环境

- Ubuntu LTS
- 已经部署过 vpsguard 的 VPS
- 需要无人值守安装 Hysteria2 的场景
- 需要保留现有 SSH、安全策略、fail2ban、UFW 基础策略的场景

## 3. 一键安装命令

建议使用 root 用户执行：

```bash
sudo -i
```

然后执行推荐的一键安装命令：

```bash
apt update && apt install -y curl && bash <(curl -fsSL https://raw.githubusercontent.com/hexa46656-creator/hysteria2-oneclick/main/install.sh)
```

## 4. 查看状态命令

```bash
bash status.sh
```

## 5. 卸载命令

```bash
bash uninstall.sh
```

## 6. 客户端配置说明

安装完成后，客户端信息会保存到：

```bash
/root/hysteria2-client.txt
```

该文件中包含服务端地址、端口、密码、自签证书说明和示例客户端配置。Hysteria2 默认使用 `443/udp`，并使用自签证书，因此客户端通常需要显式开启 `insecure: true`。

## 7. 为什么默认使用自签证书

默认使用自签证书的原因是为了实现完全无人值守安装。这样脚本不需要预先准备真实域名和证书，也不依赖外部 ACME 申请流程，适合快速部署和测试环境。

## 8. 客户端需要 insecure: true

因为自签证书没有受信任的公共 CA 链，客户端在连接时需要开启 `insecure: true` 才能跳过证书校验。

## 9. 常见故障排查

1. 服务起不来：先执行 `bash status.sh` 查看 systemd 状态和日志。
2. 端口不通：确认 `443/udp` 是否已放行，检查云厂商安全组和本机 UFW。
3. 客户端连不上：确认客户端配置中的 `server`、`port`、`password` 是否与 `/root/hysteria2-client.txt` 一致。
4. 证书报错：自签证书属于预期行为，客户端需要启用 `insecure: true`。
5. 安装失败：检查 VPS 是否能够访问软件源，或是否存在旧的 Hysteria2 配置与服务残留。

## 中文说明

### 项目简介

本项目用于在 Ubuntu VPS 上一键部署 Hysteria2。脚本会自动安装依赖、生成随机密码、写入服务端配置、输出导入链接，并把客户端信息保存到本机。

### 支持系统

- Ubuntu LTS
- 推荐使用网络正常、没有旧 Hysteria2 残留配置的 VPS

### 一键安装命令

```bash
sudo -i
apt update && apt install -y curl
bash <(curl -fsSL https://raw.githubusercontent.com/hexa46656-creator/hysteria2-oneclick/main/install.sh)
```

### 默认端口

- 默认端口：`443/udp`

### 默认 SNI

- 默认伪装域名 / 订阅 SNI：`speed.cloudflare.com`
- 默认伪装 URL：`https://speed.cloudflare.com`
- 如果你手动传入 `MASQUERADE_HOST` 或 `MASQUERADE_URL`，脚本会保留你的自定义值

### 安装完成后的客户端链接

- 客户端信息保存到：`/root/hysteria2-client.txt`
- 安装完成后，终端会显示 `hysteria2://` 导入链接和订阅链接
- 如果你使用的是支持订阅的客户端，可以优先使用订阅链接

### 二维码扫码导入

安装完成后，脚本会在终端显示二维码，并保存 PNG 文件。

- 二维码内容优先使用脚本最终生成的订阅链接
- 如果订阅链接不可用，会回退到原始 `hysteria2://` 链接
- PNG 文件保存路径：`/root/hysteria2-qr.png`

常用客户端：

- Shadowrocket
- v2rayNG
- Hiddify
- NekoBox
- Clash / Clash Verge

### 状态检查命令

```bash
bash status.sh
```

### 卸载命令

```bash
bash uninstall.sh
```

### 安全提示

- Hysteria2 当前使用自签证书，客户端通常需要开启 `insecure: true`
- 请确认 `443/udp` 已在云安全组和本机防火墙中放行
- 如果你修改了伪装域名，请先确认 DNS 解析正常

### 故障排查

1. 先执行 `bash status.sh` 查看服务状态和日志
2. 确认 `443/udp` 已放行
3. 检查 `/root/hysteria2-client.txt` 中的密码和服务器地址是否一致
4. 如果二维码无法显示，直接复制原始链接手动导入
5. 如果服务起不来，查看 `journalctl -u hysteria-server.service -n 100 --no-pager`
