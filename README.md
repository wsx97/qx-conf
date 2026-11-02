# Quantumult X 懒人配置（去广告向）

一份**只做引用、不含任何规则正文**的 Quantumult X 配置模板：分流层拦截 + 重写层净化，双层去广告，国内直连，自动测速选节点。

> 本仓库**不包含任何代理节点、订阅链接或第三方规则文件**。你需要自备机场订阅。
> 配置里的规则全部以 URL 形式引用原作者仓库，版权归各自作者所有。

---

## 一键导入

[**→ 点这里添加到 Quantumult X**](quantumult-x:///add-resource?remote-resource=%7B%22filter_remote%22%3A%5B%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fblackmatrix7%2Fios_rule_script%2Fmaster%2Frule%2FQuantumultX%2FAdvertising%2FAdvertising.list%2C%20tag%3DAdvertising%2C%20force-policy%3Dreject%2C%20update-interval%3D86400%2C%20enabled%3Dtrue%22%5D%2C%22rewrite_remote%22%3A%5B%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fddgksf2013%2FRewrite%2Fmaster%2FAdBlock%2FApplet.conf%2C%20tag%3D%E5%BE%AE%E4%BF%A1%E5%B0%8F%E7%A8%8B%E5%BA%8F%E5%87%80%E5%8C%96%2C%20update-interval%3D86400%2C%20enabled%3Dtrue%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fddgksf2013%2FRewrite%2Fmaster%2FAdBlock%2FWeiboAds.conf%2C%20tag%3D%E5%BE%AE%E5%8D%9A%E5%87%80%E5%8C%96%2C%20update-interval%3D86400%2C%20enabled%3Dtrue%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fddgksf2013%2FRewrite%2Fmaster%2FAdBlock%2FNeteaseAds.conf%2C%20tag%3D%E7%BD%91%E6%98%93%E4%BA%91%E5%87%80%E5%8C%96%2C%20update-interval%3D86400%2C%20enabled%3Dtrue%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fddgksf2013%2FRewrite%2Fmaster%2FAdBlock%2FTieBaAds.conf%2C%20tag%3D%E8%B4%B4%E5%90%A7%E5%87%80%E5%8C%96%2C%20update-interval%3D86400%2C%20enabled%3Dtrue%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fddgksf2013%2FRewrite%2Fmaster%2FAdBlock%2FAmapAds.conf%2C%20tag%3D%E9%AB%98%E5%BE%B7%E5%87%80%E5%8C%96%2C%20update-interval%3D86400%2C%20enabled%3Dtrue%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fddgksf2013%2FRewrite%2Fmaster%2FAdBlock%2FGoofishAds.conf%2C%20tag%3D%E9%97%B2%E9%B1%BC%E5%87%80%E5%8C%96%2C%20update-interval%3D86400%2C%20enabled%3Dtrue%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fddgksf2013%2FRewrite%2Fmaster%2FAdBlock%2FYoutubeAds.conf%2C%20tag%3D%E6%B2%B9%E7%AE%A1%E5%87%80%E5%8C%96%2C%20update-interval%3D86400%2C%20enabled%3Dtrue%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fddgksf2013%2FRewrite%2Fmaster%2FAdBlock%2FBingSimplify.conf%2C%20tag%3DBing%E7%AE%80%E5%8C%96%2C%20update-interval%3D86400%2C%20enabled%3Dtrue%22%5D%7D)

点开链接后 QX 会弹窗询问，确认即自动添加 **1 条分流规则 + 8 个重写模块**，参数（含 `force-policy=reject`）一并带上。

> 如果你的 QX 版本对这个 scheme 没反应（部分旧版不支持），改用下面的「快速开始」，或直接把 `QuantumultX-lazy.conf` 作为远程配置导入——效果一样。

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
2. **导入**：Quantumult X → 配置文件 → 导入本文件（或把本仓库的 raw 链接作为远程配置）。
3. **装证书（关键一步）**：QX → 设置 → MitM → 生成证书并安装 → 到 iOS「设置 → 通用 → 关于本机 → 证书信任设置」打开对该证书的**完全信任**。
   不做这步，`[rewrite_remote]` 里 8 个模块会全部静默失效——这是最常见的"配了但没用"原因。
4. 首页把策略切到「自动选择」，打开开关，刷新订阅。

## 访问加速（镜像）

配置里所有远程地址默认走 `ghproxy.net` 前缀，因为 `raw.githubusercontent.com` 在国内经常连不上。以下镜像我逐个实测过（能返回正常规则内容，非仅有响应头）：

| 镜像 | 用法 | 实测 |
|---|---|---|
| `ghproxy.net` | 前缀式：`https://ghproxy.net/https://raw.githubusercontent.com/...` | ✅ 可用（默认） |
| `gh-proxy.com` | 前缀式，同上 | ✅ 可用 |
| `ghfast.top` | 前缀式，同上 | ✅ 可用 |
| `cdn.jsdelivr.net` | 路径式：`https://cdn.jsdelivr.net/gh/用户/仓库@分支/路径` | ✅ 可用 |
| `gcore.jsdelivr.net` | 路径式 | ❌ 返回空内容 |
| `raw.gitmirror.com` | 路径式 | ❌ TLS 握手失败 |

切换方式：把 URL 里的 `https://ghproxy.net/` 前缀删掉即回到官方 raw，或整体换成上表另一个前缀。

> 这些都是第三方代理服务，你的请求会经过它们，也可能随时停服。只用来拉取**公开的规则文件**，不要用它们传输任何私密内容（比如你的订阅链接）。

## 文件说明

```
QuantumultX-lazy.conf   主配置（唯一需要改的文件）
README.md               本说明
LICENSE                  MIT
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
先换镜像（见上表）。订阅失败多半是网络问题，不是规则本身坏了。

**广告还在 / 提示策略找不到**
`force-policy=reject` 是让这份列表整体走拒绝策略的关键：列表内部自带的策略名（`Advertising`）在你的配置里并不存在。丢了这个参数，规则会因为找不到策略而不生效。

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
