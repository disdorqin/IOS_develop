# 如何获取 Apple 证书和配置信息

## 快速回答：使用 Codemagic 还需要这些吗？

**简短回答**：使用 Codemagic 可以**自动管理大部分证书**，但仍需要：

1. ✅ **Apple Developer 账户**（必须有）
2. ✅ **App Store Connect API 密钥**（用于自动发布）
3. ⚠️ **证书和配置文件**（Codemagic 可以自动创建，但需要您授权）

---

## 详细获取步骤

### 1. 获取 Team ID（团队 ID）

**位置**：Apple Developer 账户

**步骤**：
1. 访问 https://developer.apple.com
2. 登录您的 Apple ID（disdorqin@gmail.com）
3. 点击 **Account**（账户）
4. 在 **Membership** 页面找到 **Team ID**

**格式**：10 位字母数字组合，如 `A1B2C3D4E5`

---

### 2. 获取 App Store Connect API 密钥

**位置**：App Store Connect

**步骤**：
1. 访问 https://appstoreconnect.apple.com
2. 登录您的 Apple ID
3. 点击用户图标 → **Users and Access**
4. 滚动到页面底部，点击 **Keys**
5. 点击 **+** 按钮创建新密钥
6. 填写名称（如 "Codemagic API Key"）
7. 选择 **Access** 级别：
   - 勾选 **App Manager**（用于上传应用）
8. 点击 **Generate**
9. **立即下载 `.p8` 文件**（只能下载一次！）
10. 记录显示的信息：
    - **Issuer ID**（发行者 ID）
    - **Key ID**（密钥 ID）

**保存信息示例**：
```
Issuer ID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Key ID: ABCDE12345
Private Key: 下载 AuthKey_ABCDE12345.p8 文件
```

---

### 3. 获取 Certificate UUID（证书 UUID）

**方式一：Codemagic 自动创建（推荐）**

Codemagic 可以自动为您创建证书，无需手动获取！

**方式二：手动创建**

1. 访问 https://developer.apple.com
2. 进入 **Certificates, IDs & Profiles**
3. 点击 **Certificates** → **+**
4. 选择证书类型：
   - **iOS Distribution (App Store)** - 用于发布
   - **iOS Development** - 用于开发
5. 按照指引创建 CSR 文件并上传
6. 下载证书（`.cer` 文件）
7. 双击导入到钥匙串访问
8. 导出为 `.p12` 格式（包含私钥）

**获取 UUID**：
- 在 Certificates 列表中点击证书名称
- URL 中包含 UUID，如 `certificate/ABCD1234EFGH5678`
- 或使用命令：`security find-identity -v -p codesigning`

---

### 4. 获取 Provisioning Profile UUID（配置文件 UUID）

**方式一：Codemagic 自动创建（推荐）**

**方式二：手动创建**

1. 访问 https://developer.apple.com
2. 进入 **Certificates, IDs & Profiles**
3. 点击 **Profiles** → **+**
4. 选择配置文件类型：
   - **App Store** - 发布到 App Store
   - **Ad Hoc** - 测试分发
5. 选择 App ID（`com.kelivo.app`）
6. 选择证书
7. 选择设备（Ad Hoc 需要）
8. 命名并生成配置文件
9. 下载 `.mobileprovision` 文件

**获取 UUID**：
- 在 Profiles 列表中点击配置文件名称
- URL 中包含 UUID
- 或使用命令：
```bash
security find-identity -v | grep "com.kelivo.app"
```

---

## Codemagic 配置详解

### 使用 Codemagic 的两种方式

#### 方式 A：完全自动（推荐新手）

Codemagic 会自动：
- 创建 App ID
- 创建证书
- 创建配置文件
- 管理签名

**您只需要**：
1. Apple Developer 账户
2. 在 Codemagic 登录时授权

**步骤**：
1. 访问 https://codemagic.io
2. 使用 GitHub 登录
3. 导入 Kelivo 仓库
4. 在 **Workflow** 设置中：
   - 勾选 **Automatic code signing**
   - 选择您的 Apple Developer 账户
5. Codemagic 会自动处理其余事情！

#### 方式 B：使用自有证书

如果您已有证书和配置文件：

1. 在 Codemagic 的 **Workflow** 设置中：
   - 取消勾选 Automatic code signing
2. 在 **Code signing identities** 部分上传：
   - 证书（`.p12` 文件）
   - 配置文件（`.mobileprovision` 文件）
3. 在环境变量中添加：
   ```
   BUNDLE_ID: com.kelivo.app
   ```

---

## Codemagic 最小配置

如果只使用 Codemagic 的基本功能（自动签名），`codemagic.yaml` 可以简化为：

```yaml
workflows:
  ios-workflow:
    name: Kelivo iOS Build
    environment:
      flutter: 3.29.0
      xcode: latest
      ios_signing:
        automatic: true  # 自动签名
    scripts:
      - name: Get Flutter packages
        script: flutter pub get
      - name: Build iOS
        script: flutter build ios --release
```

---

## 总结：使用 Codemagic 需要什么？

| 项目 | 是否需要 | 说明 |
|------|----------|------|
| Apple Developer 账户 | ✅ 必须 | 用于创建 App 和证书 |
| App Store Connect API 密钥 | ✅ 推荐 | 用于自动发布到 TestFlight |
| Team ID | ⚠️ 自动获取 | Codemagic 会从您的账户获取 |
| 证书 | ⚠️ 自动创建 | Codemagic 可以自动创建 |
| 配置文件 | ⚠️ 自动创建 | Codemagic 可以自动创建 |
| ExportOptions.plist | ❌ 不需要 | Codemagic 自动处理 |

---

## 快速开始（推荐流程）

1. **准备 Apple Developer 账户**
   - 确保您是 Apple Developer Program 成员（$99/年）

2. **注册 Codemagic**
   - 访问 https://codemagic.io
   - 使用 GitHub 登录
   - 导入 Kelivo 仓库

3. **配置自动签名**
   - 在 Codemagic Workflow 设置中
   - 启用 **Automatic code signing**
   - 登录 Apple 账户授权

4. **（可选）配置 App Store Connect**
   - 按照前面步骤创建 API 密钥
   - 在 Codemagic 中添加环境变量

5. **触发构建**
   - 推送代码或手动触发

---

## 联系信息

- **Apple ID**: disdorqin@gmail.com
- **Codemagic**: https://codemagic.io

## 生成时间
2026/4/7