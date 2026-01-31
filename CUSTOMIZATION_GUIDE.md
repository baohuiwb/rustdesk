# RustDesk 自定义修改指南

本文档记录了 RustDesk 客户端常用的自定义修改位置，方便快速定位和修改。

---

## 1. 服务器配置

### 文件：`libs/hbb_common/src/config.rs`

| 位置 | 说明 | 示例 |
|------|------|------|
| `RENDEZVOUS_SERVERS` | ID/中继服务器地址 | `&["你的IP或域名"]` |
| `RS_PUB_KEY` | 服务器公钥 | 从服务器 `id_ed25519.pub` 获取 |
| `APP_NAME` | 应用名称（窗口标题） | `"你的品牌名"` |

### 文件：`src/common.rs`

| 函数 | 说明 |
|------|------|
| `get_custom_rendezvous_server()` | 强制返回自定义服务器地址 |
| `get_api_server_()` | 强制返回 API 地址，如 `http://你的IP:21114` |

---

## 2. 功能开关

### 文件：`libs/hbb_common/src/config.rs`

| 函数 | 说明 | 修改方法 |
|------|------|----------|
| `is_incoming_only()` | Incoming-only 模式（只显示ID） | 返回 `true` 启用 |
| `is_disable_settings()` | 禁用设置页面 | 返回 `true` 启用 |
| `is_disable_account()` | 禁用账户登录 | 返回 `true` 启用 |
| `is_disable_ab()` | 禁用地址簿 | 返回 `true` 启用 |
| `is_disable_installation()` | 禁用安装提示 | 返回 `true` 启用 |

---

## 3. 隐藏被控端连接提示窗口

### 文件：`flutter/lib/main.dart`

找到 `runConnectionManagerScreen()` 函数，修改为：

```dart
// 强制隐藏连接管理窗口
final hide = true;
gFFI.serverModel.hideCm = hide;
await hideCmWindow(isStartup: true);
```

### 文件：`flutter/lib/models/server_model.dart`

确保 `hideCm = true`（约第34行）

---

## 4. UI 文字自定义

### 文件：`src/lang/cn.rs`

| 键 | 说明 | 默认值 |
|----|------|--------|
| `Your Desktop` | 主界面标题 | `你的桌面` |
| `desk_tip` | 主界面提示文字 | `你的桌面可以通过下面的 ID 和密码访问。` |

---

## 5. 固定密码

### 文件：`libs/hbb_common/src/config.rs`

找到 `get_permanent_password()` 函数，在开头添加：

```rust
pub fn get_permanent_password() -> String {
    // 强制使用固定密码
    return "你的密码".to_owned();
    // ... 原有代码
}
```

### 默认配置（在 `LocalConfig::load()` 中添加）：

```rust
// 默认只使用固定密码验证
config.options.insert("verification-method".to_string(), "use-permanent-password".to_string());
// 默认密码验证模式（不需要点击确认）
config.options.insert("approve-mode".to_string(), "password".to_string());
```

---

## 6. 强制使用中继模式

### 文件：`libs/hbb_common/src/config.rs`

在 `LocalConfig::load()` 函数中添加：

```rust
config.options.insert("force-always-relay".to_string(), "Y".to_string());
```

---

## 7. 默认设置

### 文件：`libs/hbb_common/src/config.rs`

在 `LocalConfig::load()` 函数中可以设置默认选项：

```rust
// 默认启用 UDP 打洞
config.options.insert("enable-udp-punch".to_string(), "Y".to_string());
// 默认暗黑主题
config.options.insert("theme".to_string(), "dark".to_string());
// 默认关闭检查更新
config.options.insert("enable-check-update".to_string(), "N".to_string());
// 默认拒绝局域网发现
config.options.insert("enable-lan-discovery".to_string(), "N".to_string());
// 默认隐藏连接管理窗口
config.options.insert("allow-hide-cm".to_string(), "Y".to_string());
```

---

## 快速搜索标记

所有自定义修改位置都用 `[自定义]` 注释标记，可以在代码中搜索 `[自定义]` 快速定位。

---

## 编译方法

1. 推送代码到 GitHub
2. 打开 GitHub Actions
3. 运行 Flutter Nightly Build
4. 选择对应分支编译
