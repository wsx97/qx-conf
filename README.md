# Quantumult X 懒人配置（去广告向）

一份**只做引用、不含任何规则正文**的 Quantumult X 配置模板：分流层拦截 + 重写层净化，双层去广告，国内直连，自动测速选节点。

> 本仓库**不包含任何代理节点、订阅链接或第三方规则文件**。你需要自备机场订阅。
> 配置里的规则全部以 URL 形式引用原作者仓库，版权归各自作者所有。

---

## 特性

| 能力 | 实现方式 |
|---|---|
| 网页 / 域名级广告拦截 | `[filter_remote]` 引用 [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script) 的 `Advertising.list`（291,159 条），`force-policy=reject` |
| App 内广告净化 | `[rewrite_remote]` 引用 8 个高频模块（微信小程序 / 微博 / 网易云 / 贴吧 / 高德 / 闲鱼 / 油管 / Bing） |
| 国内直连、其余走代理 | `[filter_local]` 中 `geoip, cn, direct` + `final` 兜底 |
| 自动选最优节点 | `[policy]` 中 `url-latency-benchmark`，600s 重测、100ms 容差防抖 |
| 备用精简列表 | 注释里提供 315KB 的合并精简版，29 万条拖慢启动时切换 |

## 快速开始

1. **改订阅**：打开 `QuantumultX-lazy.conf`，把 `[server_remote]` 下的那一行注释解开，地址换成你自己的机场订阅。
2. **导入**：Quantumult X → 配置文件 → 导入本文件（或把 raw 链接作为远程配置）。
3. **装证书（关键一步）**：QX → 设置 → MitM → 生成证书并安装 → 到 iOS「设置 → 通用 → 关于本机 → 证书信任设置」打开对该证书的**完全信任**。
   不做这步，`[rewrite_remote]` 里 8 个模块会全部静默失效——这是最常见的"配了但没用"原因。
4. 首页把策略切到「自动选择」，打开开关，刷新订阅。

## 文件说明

```
QuantumultX-lazy.conf   主配置（唯一需要改的文件）
README.md               本说明
.gitignore
```

## 常见问题

**QX 启动变慢 / 占用变高**
`Advertising.list` 有 29 万条，解析需要时间。在 `[filter_remote]` 里注释掉它，改用注释中给的精简版（`adblockqxlite.conf`，315KB，每 8 小时更新）。

**某个 App 打不开、图片加载不出来**
是误伤。在该 App 的域名上放行：在 `[filter_local]` 的 `geoip, cn, direct` **上面**加一行

```ini
host-suffix, 出问题的域名, direct
```

**规则不更新 / 导入失败**
`raw.githubusercontent.com` 在国内经常连不上。把地址前缀换成任意可用镜像（例如 `https://ghproxy.net/https://raw.githubusercontent.com/...`），或改用 jsDelivr。订阅失败基本都是网络问题，不是规则本身坏了。

**要不要再叠一份别的广告列表？**
不要。多份全量列表同时订阅会大量重复，既拖慢启动也更容易误伤。二选一，或都用精简版。

## 未包含的内容（有意为之）

- **iOS 系统更新屏蔽**：会永久阻断系统更新提示，属于"想要才加"。
- **App 会员解锁**：稳定性差、随时失效，且涉及版权风险。
- **开屏广告专项模块（StartUpAds 类）**：一次改写大量 App 的开屏接口，误伤概率显著高于上面那 8 个单点模块。

## 来源与致谢

本配置仅做整合与参数编排，规则内容全部来自以下开源项目：

- [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script) — 分流规则
- [ddgksf2013/Rewrite](https://github.com/ddgksf2013/Rewrite) — App 净化重写
- [217heidai/adblockfilters](https://github.com/217heidai/adblockfilters) — 合并精简列表
- [crossutility/Quantumult-X](https://github.com/crossutility/Quantumult-X) — 官方语法样例

如果你要二次分发，请**只分发本仓库的这份引用式配置**，不要把他人的规则文件复制进自己仓库再发布。

## 免责声明

- 配置仅为个人使用整理，**不保证任何规则的有效性、准确性或持续可用**；上游规则随时可能变动或失效。
- 本项目不提供、不代理、不推荐任何代理服务或节点，请遵守你所在地区的法律法规。
- 使用本配置产生的一切后果由使用者自行承担。

## License

本仓库中的配置与文档以 MIT 协议发布；所引用的第三方规则遵循其各自仓库的授权条款。
