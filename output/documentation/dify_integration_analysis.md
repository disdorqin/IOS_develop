# Dify 集成分析报告

## 概述

本项目（Kelivo）是一个跨平台 Flutter LLM 聊天客户端，目前已有一个 `feature/dify-integration` 分支用于集成 Dify 后端。

## Dify 集成状态

### Git 分支信息
- 分支名称：`origin/feature/dify-integration`
- 最新提交：`60db13e` (安卓)
- Dify 相关提交：`7bd153b` (dify)

### 已修改的文件

根据 Git 提交记录，Dify 集成主要修改了以下文件：

| 文件路径 | 修改内容 |
|---------|---------|
| `lib/core/providers/model_provider.dart` | 添加 `ProviderKind.dify` 到 OpenAI 提供商处理 |
| `lib/core/services/api/builtin_tools.dart` | 添加 Dify 到内置工具支持 |
| `lib/desktop/setting/providers_pane.dart` | 添加 Dify 显示名称 |
| `lib/features/home/services/tool_handler_service.dart` | 添加工具处理支持 |
| `lib/features/provider/pages/provider_detail_page.dart` | 添加 Dify UI 显示 |
| `lib/features/provider/widgets/share_provider_sheet.dart` | 添加 Dify 配置编码 |
| `lib/shared/widgets/mermaid_bridge_web.dart` | Web 兼容性修复 |
| `lib/utils/avatar_cache.dart` | Web 兼容性修复 |

### ProviderKind 枚举

当前项目中的 `ProviderKind` 枚举定义在 `lib/core/providers/settings_provider.dart`：

```dart
enum ProviderKind { openai, google, claude }
```

Dify 分支将其扩展为：

```dart
enum ProviderKind { openai, google, claude, dify }
```

### Dify 集成方式

Dify 被集成作为 OpenAI 兼容的提供商：

1. **API 处理**：Dify 使用 `OpenAIProvider` 进行 API 调用
2. **配置方式**：与 OpenAI 类似的配置界面
3. **内置工具**：支持与 OpenAI 类似的内置工具配置

## Dify 后端介绍

Dify 是一个开源的 LLM 应用开发平台，提供：

- API 接口用于模型调用
- 工作流编排
- 知识库管理
- 工具集成

### Dify API 兼容性

Dify 提供 OpenAI 兼容的 API 接口：
- 基础 URL 格式：`https://your-dify-instance.com/v1`
- 支持 Chat Completions API
- 支持工具调用（Function Calling）

## 连接测试

### 测试步骤

1. 获取 Dify API Key
2. 配置 Dify 服务器地址
3. 测试 API 连接
4. 验证模型列表获取

### 配置要求

- **API Base URL**: Dify 服务器地址（如 `https://api.dify.ai/v1`）
- **API Key**: Dify 应用密钥
- **模型 ID**: 要使用的模型标识

## 项目架构

### 当前支持的提供商

| 提供商 | ProviderKind | 处理类 |
|-------|-------------|--------|
| OpenAI | `openai` | `OpenAIProvider` |
| Google | `google` | `GoogleProvider` |
| Claude | `claude` | `ClaudeProvider` |
| Dify | `dify` | `OpenAIProvider` (复用) |

### 代码结构

```
lib/
├── core/
│   ├── providers/
│   │   ├── model_provider.dart      # 提供商管理器
│   │   └── settings_provider.dart   # 设置和枚举定义
│   └── services/
│       └── api/
│           ├── chat_api_service.dart    # API 调用服务
│           └── builtin_tools.dart       # 内置工具支持
├── features/
│   ├── provider/
│   │   ├── pages/
│   │   │   └── provider_detail_page.dart  # 提供商详情页面
│   │   └── widgets/
│   │       ├── add_provider_sheet.dart    # 添加提供商
│   │       └── share_provider_sheet.dart  # 分享配置
│   └── home/
│       └── services/
│           └── tool_handler_service.dart  # 工具处理
└── desktop/
    └── setting/
        └── providers_pane.dart        # 桌面设置面板
```

## 下一步建议

1. **合并 Dify 分支**：将 `feature/dify-integration` 分支合并到主分支
2. **完善本地化**：添加 Dify 相关的本地化字符串
3. **测试验证**：在 iOS 平台上测试 Dify 连接
4. **文档更新**：更新用户文档说明 Dify 配置方法

## 生成时间
2026/4/7