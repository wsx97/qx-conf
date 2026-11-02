# Quantumult X 配置（去广告向）

基于 [Orz-3 的配置模板](https://github.com/Orz-3/QuantumultX) 重建的一份完整 QX 配置：**Surge 规则集 + 资源解析器**，去广告为主，国内直连，节点按地区分组。

> 本仓库**不含任何节点、订阅链接或第三方规则正文**，全部以 URL 引用原作者仓库。你需要自备机场订阅。

---

## 一键导入

### [**→ 点这里添加到 Quantumult X**](https://wsx97.github.io/qx-conf/)

点开是一个中转页，会在手机上唤起 QX，确认即添加 **3 条拦截列表 + 5 个重写模块**。

> ⚠️ **前提**：本配置的分流和重写全部依赖 `[general]` 里的 `resource_parser_url`（资源解析器）。一键链接**只添加资源条目、不会写解析器设置**，所以要先把整份 `QuantumultX-lazy.conf` 导进 QX，再用这个链接补资源；否则 Surge 格式的列表会解析失败。
> 直接导入整份配置的话，这些资源本来就都在里面，不需要再点。

中转页打不开时（`github.io` 在国内经常连不上），复制下面的地址粘到 Safari 地址栏回车，同样能调起 App：

```text
quantumult-x:///add-resource?remote-resource=%7B%22filter_remote%22%3A%5B%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2FConnersHua%2FRuleGo%2Fmaster%2FSurge%2FRuleset%2FExtra%2FReject%2FAdvertising.list%2C%20tag%3D%E5%B9%BF%E5%91%8A%E6%8B%A6%E6%88%AA%2C%20force-policy%3Dreject%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2FConnersHua%2FRuleGo%2Fmaster%2FSurge%2FRuleset%2FExtra%2FReject%2FTracking.list%2C%20tag%3D%E9%9A%90%E7%A7%81%E4%BF%9D%E6%8A%A4%2C%20force-policy%3Dreject%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fblackmatrix7%2Fios_rule_script%2Fmaster%2Frule%2FQuantumultX%2FAdvertisingLite%2FAdvertisingLite.list%2C%20tag%3D%E5%B9%BF%E5%91%8A%E6%8B%A6%E6%88%AALite%2C%20force-policy%3Dreject%22%5D%2C%22rewrite_remote%22%3A%5B%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2FAioneas%2FSurge%2Fmain%2FModule%2Fyoutube.aioneas.hide-shorts.sgmodule%2C%20tag%3D%E6%B2%B9%E7%AE%A1%E5%8E%BBShorts%2C%20opt-parser%3Dtrue%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2FConnersHua%2FRuleGo%2Fmaster%2FSurge%2FModule%2FBlock%2FAdvertising.sgmodule%2C%20tag%3D%E7%A5%9E%E6%9C%BA%E5%8E%BB%E5%B9%BF%E5%91%8A%2C%20opt-parser%3Dtrue%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fapp2smile%2Frules%2Fmaster%2Fmodule%2Ftieba-qx.conf%2C%20tag%3D%E8%B4%B4%E5%90%A7%E5%8E%BB%E5%B9%BF%E5%91%8A%22%2C%22https%3A%2F%2Fddgksf2013.top%2Frewrite%2FStartUpAds.conf%2C%20tag%3D%E5%A2%A8%E9%B1%BC%E5%8E%BB%E5%BC%80%E5%B1%8F%22%2C%22https%3A%2F%2Fghproxy.net%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fblackmatrix7%2Fios_rule_script%2Fmaster%2Frewrite%2FQuantumultX%2FAdvertising%2FAdvertising.conf%2C%20tag%3D%E9%BB%91%E7%8C%AB%E5%8E%BB%E5%B9%BF%E5%91%8A%22%5D%7D
```

## 这份配置是怎么组织的

| 段落 | 内容 |
|---|---|
| `[general]` | `resource_parser_url` 解析器、探测网址、DNS 排除项、`udp_drop_list = 443` |
| `[dns]` | 国内 Do53 服务器 + `circumvent-ipv4-answer`（屏蔽运营商插广告的那批 IP） |
| `[policy]` | 按地区拆分的策略组：香港 / 台湾 / 日本 / 狮城 / 美国节点，外加全球加速、苹果服务、港台番剧、国际媒体、电报代理、黑白名单 |
| `[filter_remote]` | 12 个 ConnersHua/RuleGo 的 Surge 规则集（全部 `opt-parser=true`）+ 1 个 blackmatrix7 的 `AdvertisingLite.list`（38,066 条，QX 原生） |
| `[rewrite_remote]` | 油管去 Shorts、神机去广告、贴吧去广告、墨鱼去开屏、黑猫去广告 |
| `[filter_local]` | 局域网 / `.local` 直连 + `geoip, cn, direct` + `final, 黑白名单` |

### 关键依赖：资源解析器

`resource_parser_url` 指向 KOP-XIAO 的 `resource-parser.js`（约 271KB），它在 QX 里把 **Surge / Loon 格式**的列表与模块就地转成 QX 格式。这是这套配置能用社区最大一批规则的原因——但也意味着：**解析器拉不到，所有 `opt-parser=true` 的条目都会失效**。

### 节点命名会影响策略组

地区策略组靠 tag 正则匹配，不是按顺序取。摘要：

| 组 | 匹配的节点名特征 |
|---|---|
| 香港节点 | 港、HK、Hong |
| 台湾节点 | 台、TW、Taiwan |
| 日本节点 | 本、JP、Japan |
| 狮城节点 | 坡、狮、獅、SG、Singapore |
| 美国节点 | 美、US、States、American |
| 特殊节点 | 以上都不含的其余节点 |

**如果你的节点名不含这些特征，地区组会是空的**，此时流量仍由「黑白名单」里的 `proxy` / `direct` 兜底，不会断网，但按地区分流就失效了。要适配自己的命名，改 `[policy]` 里的正则即可。

## 与模板的差异

重建时保留了模板的结构与取值，改动都记在配置注释里：

| 改动 | 原因 |
|---|---|
| 不采纳 `skip_validating_cert = true` | 会关闭证书校验 |
| 去掉 `google.cn` 302 与 `geo_location_checker` 默认启用 | 前者是模板作者的私人用法，后者会向 ip-api 上报你的出口 IP（配置里留了注释行） |
| `GoogleVoice.list`、`Proxy+.list` 改为注释 | 各自只有 1 条规则 |
| 所有远程地址加 `ghproxy.net` 前缀 | `raw.githubusercontent.com` 在国内经常连不上；把前缀删掉即回到官方地址 |
| 保留 `udp_drop_list = 443` | 模板原样：丢掉 QUIC 后视频广告退回 TCP，重写才有机会命中；代价是首帧变慢，觉得卡就注释掉 |

## 一个容易误判的地方

`ddgksf2013.top` 的模块地址**会按请求头做内容协商**：用浏览器（或 `Accept: text/html`）打开会被 302 重定向到站点首页，看起来像死链；QX 以 `text/plain` 请求时才返回真正的配置。

判断这类地址是否有效，别用浏览器点开看，改用：

```bash
curl -H "Accept: text/plain" https://ddgksf2013.top/rewrite/StartUpAds.conf | head
```

返回以 `// ==UserScript==` 开头才是配置本体。

## 常见问题

**导入后什么都没拦住**
先确认 `resource_parser_url` 那一行在不在、解析器能不能下载。所有 Surge 格式的列表都靠它转换，缺了它等于没规则。

**地区策略组显示为空**
见上面「节点命名」一节，改 `[policy]` 里的正则，或直接用「黑白名单」组。

**广告还漏**
这套用的是 ConnersHua 的规则集，该仓库**最后一次提交停在 2023-11-11**，覆盖面和时效性都不如 2026 年还在更新的列表。想换更勤的源，把 `[filter_remote]` 里 `Advertising.list` 那一行替换掉即可，其余不用动。

**看视频变卡 / 某些站点打不开**
多半是 `udp_drop_list = 443`，注释掉它试试。

**规则不更新**
先换镜像（把 `ghproxy.net` 换成 `gh-proxy.com` 或 `ghfast.top`），国内拉取失败基本都是网络问题。

**两套广告规则会重复吗**
会。ConnersHua 的 `Advertising.list`（976 条，2023 年停更）和 blackmatrix7 的 `AdvertisingLite.list`（38,066 条）有重叠，QX 匹配到第一条即停，重复项只会略增解析量、不会冲突。想精简就把 `tag=广告拦截`（ConnersHua 那条）注释掉，只留黑猫的。

**开启后 MitM 域名突然很多**
「墨鱼去开屏」437 个 + 「黑猫去广告」582 个，合计上千个域名会被解密。CPU 占用和偶发异常的概率随之上升。如果某个 App 白屏或加载卡住，按这个顺序关掉再试：黑猫去广告 → 墨鱼去开屏 → 神机去广告。

## 来源与致谢

- [Orz-3/QuantumultX](https://github.com/Orz-3/QuantumultX) — 配置模板
- [ConnersHua/RuleGo](https://github.com/ConnersHua/RuleGo) — 分流规则集与去广告模块
- [Aioneas/Surge](https://github.com/Aioneas/Surge) — 油管去 Shorts 模块
- [app2smile/rules](https://github.com/app2smile/rules) — 贴吧去广告
- [KOP-XIAO/QuantumultX](https://github.com/KOP-XIAO/QuantumultX) — 资源解析器
- [ddgksf2013](https://ddgksf2013.top) — 墨鱼去开屏模块
- [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script) — 广告分流列表与 App 内广告重写

分发时请**只分发本仓库的这份引用式配置**，不要把他人的规则文件复制进自己仓库再发布。

## 免责声明

- 配置仅为个人使用整理，**不保证任何规则的有效性、准确性或持续可用**；上游规则随时可能变动或失效。
- 本项目不提供、不代理、不推荐任何代理服务或节点，请遵守你所在地区的法律法规。
- 使用本配置产生的一切后果由使用者自行承担。

## License

本仓库中的配置与文档以 MIT 协议发布；所引用的第三方规则遵循其各自仓库的授权条款。
