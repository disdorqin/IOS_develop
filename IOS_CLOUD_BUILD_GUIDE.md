# iOS 云构建配置指南

## 概述

本文档说明如何为 Kelivo iOS 应用配置云构建。所有配置文件已创建在项目根目录。

## 已创建的配置文件

| 文件 | 用途 |
|------|------|
| `ios_cloud_build_config.json` | 云构建配置主文件 |
| `ios/ExportOptions.plist` | App Store 导出配置 |
| `ios/ExportOptions_AdHoc.plist` | Ad Hoc 测试导出配置 |
| `codemagic.yaml` | Codemagic CI/CD 配置 |
| `.github/workflows/ios-build.yml` | GitHub Actions 配置 |

## 配置步骤

### 1. Apple Developer 账户配置

登录 [Apple Developer](https://developer.apple.com) 并创建以下内容：

#### 1.1 App ID
- 进入 Certificates, IDs & Profiles
- 创建 App ID: `com.kelivo.app`
- 启用所需功能（Push Notifications, Sign in with Apple 等）

#### 1.2 证书
- **Distribution Certificate**: 用于 App Store 发布
- **Development Certificate**: 用于开发测试

#### 1.3 配置文件 (Provisioning Profiles)
- **App Store Profile**: 用于提交到 App Store
- **Ad Hoc Profile**: 用于测试分发

### 2. App Store Connect 配置

登录 [App Store Connect](https://appstoreconnect.apple.com)：

#### 2.1 创建 API 密钥
1. 进入 Users and Access → Keys
2. 生成新的 API 密钥
3. 记录以下信息：
   - **Issuer ID**
   - **Key ID**
   - 下载的 `.p8` 私钥文件

### 3. 替换配置占位符

#### 在 `ios_cloud_build_config.json` 中替换：
```json
{
  "team_id": "YOUR_TEAM_ID",
  "app_store_connect": {
    "issuer_id": "YOUR_ISSUER_ID",
    "key_id": "YOUR_KEY_ID",
    "private_key_path": "./AuthKey_YOUR_KEY_ID.p8"
  }
}
```

#### 在 `ios/ExportOptions.plist` 中替换：
```xml
<key>teamID</key>
<string>YOUR_TEAM_ID</string>

<key>provisioningProfiles</key>
<dict>
    <key>com.kelivo.app</key>
    <string>YOUR_PROVISIONING_PROFILE_NAME</string>
</dict>
```

#### 在 `codemagic.yaml` 中替换：
```yaml
environment:
  vars:
    APP_STORE_CONNECT_ISSUER_ID: YOUR_ISSUER_ID
    APP_STORE_CONNECT_KEY_IDENTIFIER: YOUR_KEY_ID
```

### 4. 选择云构建平台

#### 方案一：Codemagic（推荐）

Codemagic 是专业的 Flutter CI/CD 服务。

**步骤：**
1. 访问 https://codemagic.io
2. 使用 GitHub 登录
3. 导入 Kelivo 仓库
4. 上传配置文件 `codemagic.yaml`
5. 在 Codemagic 界面配置环境变量：
   - `APP_STORE_CONNECT_PRIVATE_KEY`
   - `CERTIFICATE_PRIVATE_KEY`
6. 触发构建

**优点：**
- 专为 Flutter 优化
- 支持自动签名和发布
- 免费额度充足

#### 方案二：GitHub Actions

**步骤：**
1. 确保仓库已启用 GitHub Actions
2. 配置文件已在 `.github/workflows/ios-build.yml`
3. 在 Settings → Secrets 中添加密钥：
   - `APP_STORE_CONNECT_API_KEY`
   - `APP_STORE_CONNECT_ISSUER_ID`

**注意：** GitHub Actions 需要 macOS 运行器，可能需要付费。

### 5. 本地构建测试

在云构建之前，建议先在本地测试构建：

```bash
# 无签名构建（测试用）
flutter build ios --release --no-codesign

# 完整构建（需要证书）
flutter build ipa --export-options-plist=ios/ExportOptions.plist
```

## 联系信息

- **Apple ID**: disdorqin@gmail.com
- **通知邮箱**: disdorqin@gmail.com

## 常见问题

### Q: 如何获取 Team ID?
A: 登录 Apple Developer 账户，在 Membership 页面可以找到 Team ID。

### Q: 证书过期怎么办？
A: 在 Apple Developer 中心重新创建证书，并更新配置文件。

### Q: 构建失败提示签名错误？
A: 检查：
1. 证书是否有效
2. 配置文件是否包含正确的 Bundle ID
3. ExportOptions.plist 配置是否正确

### Q: 如何测试 Ad Hoc 分发？
A: 使用 `ios/ExportOptions_AdHoc.plist` 进行构建，然后将 IPA 分发给测试设备。

## 参考链接

- [Codemagic 文档](https://docs.codemagic.io/)
- [Flutter iOS 部署文档](https://docs.flutter.dev/deployment/ios)
- [App Store Connect API 文档](https://developer.apple.com/documentation/appstoreconnectapi)

## 生成时间
2026/4/7