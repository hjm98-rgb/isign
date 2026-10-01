# iSign 云签

面向个人开发者的 iOS 应用签名与分发工具。导入你的开发者证书，管理已签名应用列表，云端打包并生成可点击安装的链接，手机 Safari 打开即装。

## 仓库结构

```
.
├── .github/workflows/   # GitHub Actions 云签名打包工作流
├── isign-site/          # 官网（静态落地页）
├── webapp/              # 签名管理后台（网页应用）
└── README.md
```

## 快速开始（云签名打包）

仓库内置基于 **GitHub Actions** 的云签名工作流 `cloud-sign.yml`，无需 Mac、无需本地安装签名工具，在云端完成 IPA 重签名。

### 准备证书

1. 从 Apple Developer 导出你的证书与描述文件：
   - `.p12`（证书私钥 + 证书）
   - `.mobileprovision`（描述文件）
2. **务必不要把证书直接提交到仓库**。将证书内容存入 GitHub Secrets：

   ```bash
   # 在仓库 Settings → Secrets and variables → Actions 中添加：
   # P12_BASE64     = base64 编码的证书文件（.p12）
   # MOBILEPROV_BASE64 = base64 编码的描述文件（.mobileprovision）
   # P12_PASSWORD   = 证书密码
   ```

   ```bash
   base64 -w0 cert.p12        # 生成 P12_BASE64
   base64 -w0 app.mobileprovision  # 生成 MOBILEPROV_BASE64
   ```

### 触发打包

1. 在仓库 **Actions → cloud-sign → Run workflow**：
   - `ipa_url`：待签名 IPA 的下载地址（或上传到仓库的路径）
   - `display_name`：应用显示名称
   - `bundle_id`：Bundle ID（需与描述文件匹配）
2. 工作流在云端用 `zsign` 完成重签名。
3. 产物 `signed.ipa` 通过 **workflow artifact** 下载，即可用于 OTA 分发安装。

## OTA 安装

把签名后的 `.ipa` 与描述文件部署到 HTTPS 服务，生成 `manifest.plist`，用 iPhone Safari 打开安装链接即可装到桌面：

```
itms-services://?action=download-manifest&url=https://your-host/manifest.plist
```

首次安装后到「设置 → 通用 → VPN 与设备管理」信任对应开发者证书即可正常打开。

## 合规声明

- 请仅使用你**拥有授权**的开发者证书与应用进行签名。
- 个人免费 Apple ID 的签名有设备数与有效期限制（约 7 天/10 台设备）；付费开发者账号（99 美元/年）无此限制。
- iSign 仅用于个人/企业内部自分发，不经过 App Store 审核，无法上架。
- 请遵守 Apple 相关条款与你所在地区的法律法规。

## 安全提示

`.p12` 包含你的证书私钥，任何拿到私钥的人都能以你的身份签名。请通过 GitHub Secrets 存储，切勿直接提交到仓库。

## License

开源项目，数据完全自主可控。
