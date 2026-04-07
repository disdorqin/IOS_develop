# 项目结构分析

## 项目概述

**Kelivo** - 一个跨平台 Flutter LLM 聊天客户端

### 支持平台
- Android / iOS / Harmony
- Windows / macOS / Linux

### 核心功能
- 多 LLM 供应商支持 (OpenAI, Gemini, Anthropic 等)
- MCP (Model Context Protocol) 集成
- 多语言支持 (中文/英文)
- 深色模式
- 语音 TTS 支持
- 网络搜索集成

---

## 目录结构

```
electric_chatapp/
├── lib/                          # 主要 Dart 代码
│   ├── main.dart                 # 应用入口
│   ├── core/                     # 核心模型和服务
│   ├── desktop/                  # 桌面端特定代码
│   ├── features/                 # 功能模块
│   ├── icons/                    # 图标资源
│   ├── l10n/                     # 本地化文件 (ARB)
│   ├── shared/                   # 共享组件
│   ├── theme/                    # 主题配置
│   └── utils/                    # 工具类
├── dependencies/                 # 本地路径依赖
│   ├── flutter_tts/
│   ├── flutter-permission-handler/
│   ├── mcp_client/
│   └── tray_manager/
├── test/                         # 测试文件
├── android/                      # Android 平台配置
├── ios/                          # iOS 平台配置
├── macos/                        # macOS 平台配置
├── windows/                      # Windows 平台配置
├── linux/                        # Linux 平台配置
├── web/                          # Web 平台配置
├── assets/                       # 资源文件
└── docx/                         # 文档截图
```

---

## 关键文件

### 配置文件
| 文件 | 用途 |
|------|------|
| `pubspec.yaml` | 项目依赖和 Flutter 配置 |
| `l10n.yaml` | 本地化配置 |
| `analysis_options.yaml` | Dart 代码分析规则 |
| `flutter_launcher_icons.yaml` | 应用图标配置 |

### 本地化文件 (4 种语言)
- `lib/l10n/app_en.arb` - 英文
- `lib/l10n/app_zh.arb` - 简体中文
- `lib/l10n/app_zh_Hans.arb` - 简体中文 (正式)
- `lib/l10n/app_zh_Hant.arb` - 繁体中文

### 入口文件
- `lib/main.dart` - 主入口
- `lib/desktop/desktop_home_page.dart` - 桌面端入口
- `lib/features/home/pages/home_page.dart` - 移动端/共享聊天页面

---

## 架构说明

### 平台路由
```
main.dart (_selectHome)
├── macOS/Windows/Linux → DesktopHomePage
└── Android/iOS → HomePage
```

### 代码分层
1. **core/** - 数据模型、服务、提供者
2. **features/** - 功能模块 (home, chat, settings 等)
3. **shared/** - 跨功能共享的 UI 组件
4. **desktop/** - 桌面特定功能 (托盘、热键、窗口控制)
5. **theme/** - 主题和颜色令牌

---

## 开发注意事项

### 必须遵守的规则
1. 所有用户可见文本必须本地化 (4 个 ARB 文件同步更新)
2. 生成的代码必须通过命令维护 (`flutter gen-l10n`, `build_runner`)
3. 代码修改后必须格式化 (`dart format`)
4. 提交前必须运行分析和测试

### 常用命令
```bash
# 获取依赖
flutter pub get

# 生成本地化文件
flutter gen-l10n

# 生成 Hive 模型代码
dart run build_runner build --delete-conflicting-outputs

# 代码格式化
dart format .

# 代码分析
flutter analyze

# 运行测试
flutter test
```

---

## 生成时间
2026/4/7