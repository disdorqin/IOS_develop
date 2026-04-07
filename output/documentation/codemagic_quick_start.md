# Codemagic 快速开始指南

## Codemagic 免费额度

### 免费计划（Builder Plan）

- **构建时间**: 每月 500 分钟
- **并行构建**: 1 个
- **构建时长限制**: 60 分钟/次
- **适用**: 个人开发者和小型项目

### 免费额度够用吗？

对于 Kelivo 项目：
- 每次 iOS 构建约 15-20 分钟
- 每月可以构建约 25-30 次
- **足够日常开发和测试发布**

---

## 使用 Codemagic 还需要那些占位符配置吗？

### 简短回答

**使用 Codemagic 自动签名时，大部分配置可以省略！**

### 详细对比

| 配置项 | 手动配置 | Codemagic 自动签名 |
|--------|----------|-------------------|
| Apple Developer 账户 | ✅ 必须 | ✅ 必须 |
| Team ID | ✅ 手动填写 | ❌ 自动获取 |
| 证书 | ✅ 手动创建 | ❌ 自动创建 |
| 配置文件 | ✅ 手动创建 | ❌ 自动创建 |
| ExportOptions.plist | ✅ 手动配置 | ❌ 自动生成 |
| App Store Connect API 密钥 | ✅ 用于自动发布 | ✅ 用于自动发布（可选） |

---

## 最简配置步骤（推荐）

### 步骤 1: 准备 Apple Developer 账户

确保您是 Apple Developer Program 成员（$99/年）

### 步骤 2: 注册 Codemagic

1. 访问 https://codemagic.io
2. 使用 GitHub 登录
3. 导入 Kelivo 仓库（`github.com/disdorqin/IOS_develop`）

### 步骤 3: 配置自动签名

1. 在 Codemagic 项目页面，点击 **Settings**
2. 滚动到 **iOS code signing** 部分
3. 勾选 **Automatic code signing**
4. 点击 **Connect with Apple** 并登录 Apple 账户
5. 授权 Codemagic 访问您的 Apple Developer 账户

### 步骤 4: 添加环境变量（可选，用于自动发布）

如果希望自动发布到 TestFlight：

1. 在 **Environment variables** 部分添加：
   ```
   APP_STORE_CONNECT_ISSUER_ID: 您的 Issuer ID
   APP_STORE_CONNECT_KEY_IDENTIFIER: 您的 Key ID
   APP_STORE_CONNECT_PRIVATE_KEY: 您的私钥内容（.p8 文件内容）
   ```

### 步骤 5: 触发构建

1. 点击 **Start new build**
2. 选择分支（如 `main`）
3. 点击 **Start build**

---

## 简化后的 codemagic.yaml

使用自动签名时，配置文件可以非常简洁：

```yaml
# codemagic.yaml - 简化版
workflows:
  ios-workflow:
    name: Kelivo iOS Build
    max_build_duration: 60
    environment:
      flutter: 3.29.0
      xcode: latest
      ios_signing:
        automatic: true  # 启用自动签名！
    
    scripts:
      - name: 获取 Flutter 依赖
        script: |
          flutter pub get
      
      - name: 安装 CocoaPods
        script: |
          cd ios && pod install && cd ..
      
      - name: 构建 iOS
        script: |
          flutter build ios --release
    
    artifacts:
      - build/ios/ipa/*.ipa
      - build/ios/archive/Runner.xcarchive
    
    publishing:
      email:
        recipients:
          - disdorqin@gmail.com
        notify:
          success: true
          failure: true
```

---

## 如果只需要测试构建（不发布）

如果只是想测试构建功能，甚至不需要配置 App Store Connect：

### 最小配置

```yaml
# 最小配置 - 仅构建
workflows:
  ios-workflow:
    name: Kelivo iOS Build
    environment:
      flutter: 3.29.0
      ios_signing:
        automatic: true
    scripts:
      - flutter pub get
      - cd ios && pod install
      - flutter build ios --release
```

这样配置后：
- ✅ 可以构建 IPA 文件
- ✅ 可以下载构建产物
- ❌ 不能自动发布到 TestFlight

---

## Codemagic 免费额度使用技巧

### 1. 仅在必要时触发构建

```yaml
# 仅在打标签时构建
triggering:
  events:
    - tag
  tag_patterns:
    - pattern: 'v*'
      include: true
```

### 2. 使用缓存加速构建

```yaml
cache:
  cache_paths:
    - $HOME/.pub-cache
    - Pods
```

### 3. 设置构建超时

```yaml
max_build_duration: 60  # 60 分钟超时
```

---

## 总结

### 使用 Codemagic 自动签名时：

**需要准备的：**
1. Apple Developer 账户（$99/年）
2. Codemagic 账户（免费）
3. GitHub 仓库

**Codemagic 自动处理的：**
- Team ID
- 证书创建
- 配置文件创建
- ExportOptions.plist

**可选配置的：**
- App Store Connect API 密钥（用于自动发布到 TestFlight）

### 推荐流程

1. 注册 Codemagic（免费）
2. 导入 GitHub 仓库
3. 启用自动签名
4. 推送代码触发构建
5. 下载 IPA 文件或自动发布到 TestFlight

---

## 联系信息

- **Apple ID / 通知邮箱**: disdorqin@gmail.com
- **Codemagic**: https://codemagic.io
- **Codemagic 定价**: https://codemagic.io/pricing/

## 生成时间
2026/4/7