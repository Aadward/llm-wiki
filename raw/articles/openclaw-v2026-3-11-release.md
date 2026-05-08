---
source_url: https://cloud.tencent.com/developer/article/2648607
ingested: 2026-05-09
sha256: 4b1057aca183fcf1bfaef79b7c60b12a01dfc8cc2092567b34cd5e24b375b0c0
---

# OpenClaw v2026.3.11 官方发布详情

## 发布信息
- **版本**: v2026.3.11（Immutable Release）
- **发布日期**: 2026-03-12
- **官方来源**: https://cloud.tencent.com/developer/article/2648607

## 安全更新（Security）

### Gateway/WebSocket 安全强化
- 强制所有浏览器来源连接进行源验证，无论是否存在代理头
- 从根源阻断可信代理模式下可能的跨站 WebSocket 劫持
- 关闭潜在路径漏洞，确保未被信任的跨域无法获得 operator.admin 访问权限

### system.run 安全机制
- 所有基于审批的解释器与运行时命令若无法严格绑定唯一的本地文件操作对象，将自动拒绝执行
- 防止运行时命令被外部输入劫持

### 插件运行安全限制
- 未认证的插件 HTTP 路由不再继承网关的管理权限
- admin 级方法如 sessions.delete 将被完全阻断
- 保证插件仅在授权范围内执行

### session_status 安全优化
- 强化沙盒会话树的可见性与访问控制
- 防止子代理会话读取或更改父级的元数据或模型配置
- 从结构上封闭沙盒层级的越界访问

### 节点安全增强
- 节点代理工具仅限所有者使用
- 非所有者无法通过共享工具集触发节点审批或调用

### 外部内容防护
- 新增空白分隔的 EXTERNAL UNTRUSTED CONTENT 边界标识
- 与下划线分隔标识同级识别
- 彻底防止提示包装绕过标记清理机制

### 网关认证强化
- 当本地 gateway.auth 配置的 SecretRefs 不可用时，拒绝使用远程凭据进行回退
- 杜绝本地模式下的凭据错误继承

### 配置写入访问控制
- 强制 /config 及基于 config 的 /allowlist 编辑检查来源与目标账户范围
- 防止兄弟账户间的越权修改

### session reset 认证隔离
- 拆分 /new 与 /reset 请求路径
- 将高级会话重置 RPC 仅限 admin 访问
- 防止普通写权限调用会话重置

## 功能新增与主要变化（Changes）

### 模型与 OpenRouter 扩展
- 新增临时模型 **Hunter Alpha** 与 **Healer Alpha**
- 在 OpenRouter 内置目录中可直接调用

### iOS 端体验升级
- 主页画布新增欢迎屏幕与实时代理概览
- 支持连接、重新连接或前台恢复时自动刷新
- 浮动控制替换为底部停靠工具栏，贴合小屏手机适配
- 聊天内容直接打开至主会话而非独立 ios 虚拟会话

### macOS 端改进
- 聊天 UI 新增模型选择器，显式思考等级选项可在重启后持久保存
- 会话模型同步机制加强，避免提供者感知模型漂移
- 新增入门检测机制，识别需要共享认证令牌的远程网关
- LaunchAgent 安装路径安全加固，防止组/全局可写导致的启动失败

### Onboarding 与集成扩展
- **Ollama**：支持本地与云端+本地两种模式，可浏览完成注册
- **OpenCode**：新增 Go 语言提供者，与 Zen 共享设置流程，运行时层面分离

### Memory 模块增强
- 支持可选图像与音频索引（Gemini gemini-embedding-2-preview）
- 增加严格回退机制与基于作用域的重索引
- Memory Gemini 新增输出维度可配功能

### Discord 功能优化
- 自动创建线程时可配置 autoArchiveDuration 参数
- 支持 1小时、1天、3天、1周四种归档时间

### 执行与命令行增强
- 子命令环境增加 OPENCLAW_CLI 标识
- 归档与提取机制强化，预防符号链接逃逸
