---
title: 代理工具归纳
date:  2026/6/12
categories: “代理”
---

# 四大代理软件全方位横向对比与深度解析指南

## 🛠️ 一、四大代理软件核心要素横向对比表

| 维度 | 小火箭 (Shadowrocket) | Clash Verge (Rev) | v2rayN | Nekobox (安卓) |
| :--- | :--- | :--- | :--- | :--- |
| **平台** | iOS / iPadOS / macOS (M系列) | Windows / macOS / Linux | Windows | Android (Nekobox) |
| **核心内核 (Kernel)** | 独立自主开发内核 (闭源) | **Mihomo** (原 Clash.Meta) | **Xray-core** (默认) / sing-box / Mihomo | **sing-box**/ Xray-core |
| **配置文件格式** | 专属 `.conf` 规则文件 / 单行 URL 列表 | **YAML 格式** (`.yaml` / `.yml`) | **JSON 格式** / 单行 URL 列表 | **sing-box JSON 格式** / 自定义配置 |
| **订阅导入形式** | 1. Base64单行URL列表<br>2. 自动解析 Clash YAML 订阅<br>3. `.conf` 规则文本 | 完整的配置文件（包含节点、策略组和规则的 **YAML 文本**） | 1. Base64单行URL列表<br>2. 新版支持导入定制 JSON | 1. Base64单行URL列表<br>2. sing-box JSON 订阅<br>3. 直接解析 Clash 订阅 |
| **主流协议支持** | SS, SSR, VMess, VLESS, Trojan, Snell, TUIC, Hysteria 1/2, WireGuard | SS, SSR, VMess, VLESS (Reality), Trojan, Snell, TUIC v5, Hysteria 1/2, WireGuard | VMess, VLESS (Reality), Trojan, SS, SSR, TUIC, Hysteria 1/2 (视内核而定) | VLESS (Reality), VMess, Trojan, SS, SSR, TUIC, Hysteria 1/2, NaïveProxy |
| **分流与策略组** | 支持丰富的规则（Domain, IP 等），策略组支持手动选择或延迟测试，但配置相对偏轻量。 | **极其强大。** 拥有高度自动化的策略组（延迟自动切换、负载均衡），分流规则支持 Rule Provider 远程更新。 | 分流主要依靠内置的路由规则（直连/代理/阻止），新版通过多内核可实现较复杂的路由，但界面化策略组较弱。 | 基于 sing-box 路由。安卓端内置了类似 Clash 仪表盘的 Web 界面（如 yacd-meta），支持可视化的策略组选择。 |
| **特有优势/补充** | 苹果生态神仙软件，买断制，更新极快，几乎免配置，对新协议跟进最迅速。 | 界面美观，支持扩展脚本（Script/Merge），是目前桌面端替代传统 Clash 的绝对主力。 | 传统的 Windows 客户端，多内核切换非常灵活，适合喜欢折腾核心参数的电脑用户。 | 移动端（安卓）目前对新协议（如 Reality, Hysteria 2）支持最完美的底层工具之一，性能极高、省电。 |

---

## 🔍 二、核心痛点深度解析

### 1. 订阅链接与配置文件的转换（跨客户端切换指南）
跨客户端切换时，最核心的痛点在于 **“单行 URL 列表”** 与 **“完整配置文件”** 的逻辑对立：

* **单行 URL 列表（小火箭 / v2rayN / Nekobox）：**
  订阅链接返回的是一串密密麻麻的 Base64 编码文本。解密后是一行行的 `vmess://...`、`vless://...` 或 `ss://...`。这些软件只需要**节点信息**，分流规则和策略组是软件客户端自己内置或单独配置的。
* **完整配置文件（Clash Verge）：**
  订阅链接返回的是一个结构严密的 **YAML 文件**。这个文件里不仅有节点，还强制包含了 `proxy-groups`（策略组，如：自动选择、香港节点）和 `rules`（分流规则）。Clash 客户端本身是个“空壳”，所有行为完全听从这个 YAML 文件的指挥。

> 💡 **转换避坑指南**
> * **从 Clash Verge 切换到 其它三者：** 小火箭、v2rayN 和 Nekobox 的新版本通常可以直接识别 Clash 的 YAML 订阅链接，并自动提取里面的节点信息。直接粘贴进去通常即可直接识别。
> * **从 其它三者 切换到 Clash Verge：** Clash 无法直接读取纯节点列表（Base64）。你必须通过 **“订阅转换网站”（Subconverter）**，将普通的订阅链接转换成 Clash 格式，补充策略组和分流规则后生成 YAML 链接。

### 2. 内核区别 (Kernel)
内核决定了软件的性能上限以及对新协议的兼容性：
* **小火箭内核：** 闭源独立开发。开发者效率极高，开源社区一有什么新协议，往往能几天内独自手写代码实现并推送到 App Store。
* **Mihomo 内核 (Clash Verge Rev)：** 目前开源界最火的 Clash 分支内核（原名 Clash.Meta）。全面兼容老 Clash，并加入了对 VLESS Reality、TUIC、Hysteria 2 等新协议的原生支持。
* **Xray-core (v2rayN 的主力)：** 它是 VLESS 协议和 **Reality 技术的发源地**。如果你使用的节点带有 XTLS 或 Reality 尖端防封锁技术，Xray-core 是最正宗、最稳定的底层支持者。
* **sing-box (Nekobox 的主力)：** 下一代网络通用核心。最大的特点是**运行效率极高、内存占用极小、非常省电**，在移动端（安卓）表现优异。

### 3. 主流协议与分流策略
* **协议支持：** 目前在 2026 年，各大客户端对 VLESS-Reality、Hysteria 2、TUIC v5 等基于 UDP 或尖端伪装的协议均已全线支持。Nekobox 凭借 sing-box 底层对吞吐量优化极佳；Clash Verge 则通过策略组实现了高速与低延迟的完美调度。
* **策略组表现：** Clash Verge 是当之无愧的规则策略王者，支持多层嵌套与自动化切换。小火箭和 Nekobox 提供了轻量但足够高效的配置。v2rayN 的分流规则相对扁平，通常在底部进行“核心路由模式”的一键切换。
* 转换订阅：https://linuxdo.icmpmiao.cc/

---
