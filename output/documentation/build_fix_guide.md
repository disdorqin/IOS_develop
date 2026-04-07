# Codemagic 构建错误修复指南

## 错误原因

Codemagic 构建失败，错误信息：
```
Because Kelivo depends on flutter_localizations from sdk which depends on intl 0.20.2, intl 0.20.2 is required.
So, because Kelivo depends on intl ^0.19.0, version solving failed.
```

**原因**：Codemagic 使用的 Flutter SDK 版本（3.29.0）需要 `intl 0.20.2`，但项目配置要求 `intl ^0.19.0`。

## 解决方案

### 方案一：使用 dependency_overrides（推荐）

在 `pubspec.yaml` 中添加 `dependency_overrides` 强制使用 `intl 0.20.2`：

```yaml
dependency_overrides:
  # 解决 intl 版本冲突
  intl: 0.20.2
```

**已修复**：`pubspec.yaml` 已更新，包含此覆盖配置。

### 方案二：升级 Flutter SDK 要求

将 `pubspec.yaml` 中的 SDK 要求升级到与 Codemagic 匹配：

```yaml
environment:
  sdk: ^3.29.0  # 与 Codemagic 一致
```

## 已执行的修复

### 1. 更新 pubspec.yaml

```yaml
dependency_overrides:
  # Prevent the location from being accessed continuously on Windows
  permission_handler_windows:
    path: ./dependencies/flutter-permission-handler/permission_handler_windows
  
  # Fix intl version conflict for Codemagic CI/CD
  # flutter_localizations requires intl 0.20.2, but we depend on ^0.19.0
  # This override allows both to work by using the newer version
  intl: 0.20.2
```

### 2. 更新 codemagic.yaml

添加 `flutter clean` 步骤确保干净的构建环境：

```yaml
scripts:
  - name: 清理并获取 Flutter 依赖
    script: |
      flutter clean
      flutter pub get
```

## 下一步操作

### 在 Codemagic 上重新构建

1. **推送修复后的代码到 GitHub**
   ```bash
   git add pubspec.yaml
   git commit -m "fix: Add intl override to resolve Codemagic build conflict"
   git push
   ```

2. **Codemagic 会自动触发新构建**
   - 或者手动点击 **Start new build**

3. **检查构建日志**
   - 确保 `flutter pub get` 成功
   - 检查是否有其他依赖冲突

## 其他可能的错误

### 如果仍然失败，检查：

1. **本地依赖路径**
   - 确保 `dependencies/` 目录已推送到 GitHub
   - 检查路径依赖是否正确

2. **私有子模块**
   - 如果有私有依赖，确保 Codemagic 有访问权限

3. **Flutter 版本不匹配**
   - 在 Codemagic 环境变量中指定 Flutter 版本
   ```
   flutter: 3.29.0
   ```

## 验证本地构建

在推送之前，建议先在本地验证：

```bash
# 清理并获取依赖
flutter clean
flutter pub get

# 本地测试构建（无签名）
flutter build ios --release --no-codesign
```

如果本地构建成功，Codemagic 也应该成功。

## 联系信息

- **Apple ID / 通知邮箱**: disdorqin@gmail.com
- **Codemagic 支持**: https://codemagic.io/support/

## 生成时间
2026/4/7