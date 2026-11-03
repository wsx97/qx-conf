# Quantumult X 配置（去广告向）

基于 [Orz-3 的配置模板](https://github.com/Orz-3/QuantumultX) 重建的一份完整 QX 配置：**Surge 规则集 + 资源解析器**，去广告为主，国内直连，节点按地区分组。

> 本仓库**不含任何节点、订阅链接或第三方规则正文**，全部以 URL 引用原作者仓库。你需要自备机场订阅。

---

## 两个版本

仓库里有两份**互相独立**的配置，各自单独导入、互不依赖：

| 文件 | 规模 | 适合谁 |
|---|---|---|
| `QuantumultX-lazy.conf` | 16 条分流 / 8 条重写 / 12 策略组；约 **5.9 万条**规则，MITM 约 770 域名 | 要覆盖面最大，不介意冷启动几秒 |
| `QuantumultX-lite.conf` | 8 条分流 / 2 条重写 / 4 策略组；约 **1.07 万条**规则（−82%），MITM 约 726 域名，**零解析器依赖** | 要启动快、误伤少、内存低 |

**精简版的取舍原则**：只删「删掉只是变慢」的列表，保留「删掉会直接功能失效」的。

| 删掉 | 保留（硬依赖） |
|---|---|
| AdvertisingLite 38,066（占完整版规则 64%）、Proxy 7,502、GlobalMedia 2,341、BiliBili 127、Telegram 40、WeChat 43、AWAvenue 665、番茄小说 29 | AI 四表（砍掉 ChatGPT 会报"地区不支持"）、Apple（砍掉 App Store/iCloud 走节点）、TikTok、Direct、217heidai 广告表 |

代价：广告拦截率从约 85% 降到 **60–70%**；流媒体不再分区。收益：规则量 −82%、远程资源 21 → 10、策略组 12 → 4、没有 `resource_parser_url` 这个启动失败点。

> **两份不能同时启用**——QX 一次只加载一份配置，切换就是在「配置文件」里换文件。

## 导入地址

**完整版**

```text
https://ghproxy.net/https://raw.githubusercontent.com/wsx97/qx-conf/main/QuantumultX-lazy.conf
```

**极致精简版**

```text
https://ghproxy.net/https://raw.githubusercontent.com/wsx97/qx-conf/main/QuantumultX-lite.conf
```

## 完整版：懒人配置链接

一条地址把整份配置加进 QX —— 分流规则、重写模块、策略组、DNS 设置全在里面：

```text
https://ghproxy.net/https://raw.githubusercontent.com/wsx97/qx-conf/main/QuantumultX-lazy.conf
```

**导入步骤**

1. 复制上面的地址
2. QX → **配置文件** → 右上角「+」→ 添加远程配置 → 粘贴 → 确认
3. QX → **服务器** → 添加你自己的机场订阅（**订阅不在本仓库，需自备**）
4. 回首页把策略组切到「黑白名单」或对应地区组，打开开关

在手机上也可以打开 [引导页](https://wsx97.github.io/qx-conf/) 一键复制地址。

> **为什么订阅要单独加**：配置里的 `[server_remote]` 只留了占位注释。把私人订阅写进公开仓库既会泄露，也会在配置更新时被覆盖。QX「服务器」页添加的节点会自动进入 `[policy]` 的地区策略组（靠节点名里的 港/HK、台/TW、日/JP、坡/SG、美/US 关键词匹配）。
>
> 以前那种「只添加广告资源」的一键链接已经移除，现在统一走整份配置。

## 这份配置是怎么组织的

| 段落 | 内容 |
|---|---|
| `[general]` | `resource_parser_url` 解析器、探测网址、DNS 排除项、`udp_drop_list = 443` |
| `[dns]` | 国内 Do53 服务器 + `circumvent-ipv4-answer`（屏蔽运营商插广告的那批 IP） |
| `[policy]` | 按地区拆分的策略组：香港 / 台湾 / 日本 / 狮城 / 美国节点，外加全球加速、苹果服务、港台番剧、国际媒体、电报代理、黑白名单 |
| `[filter_remote]` | **11 个 blackmatrix7 QX 原生列表**（含 Privacy / 4×AI / WeChat）+ **1 个 AWAvenue**（`opt-parser=true`）+ 1 个 blackmatrix7 的 `AdvertisingLite.list`（38,066 条，承担广告拦截主责）+ 1 个 zqzess 的番茄小说 `.snippet`（29 条）+ 217heidai 的 `adblockqxlite`（8,480 条，8 小时重建）+ AWAvenue 的广告 SDK 列表（665 条，需 opt-parser） |
| `[rewrite_remote]` | 黑猫去广告、墨鱼去开屏、番茄小说 + **2 个 App 专项**（字节系 / 小红书）；喜马拉雅 / 大众点评 / 网易云已按使用情况关闭（留行备查） |
| `[filter_local]` | 局域网 / `.local` 直连 + `geoip, cn, direct` + `final, 黑白名单` |

### IPv6

配置里跟 IPv6 有关的三处：

| 位置 | 内容 |
|---|---|
| `[dns]` | 除了 3 个 IPv4 上游，另加阿里 `[2400:3200::1]:53` 与 DNSPod `[2402:4e00::]:53` 两个 IPv6 上游（**必须方括号+端口**，裸地址会被 QX 当成端口解析而报语法错误） |
| `[filter_local]` | `::1/128`（环回）、`fe80::/10`（链路本地）、`fc00::/7`（内网 ULA）、`ff00::/8`（组播）全部直连 |
| `[general]` | 没有写 `no-ipv6`，所以 AAAA 查询是放行的（QX 里 `no-ipv6` 会直接让 AAAA 失败） |

**一个必须知道的限制**：`geoip, cn, direct` 只对 **IPv4** 生效，QX 没有 IPv6 版的 geoip。所以国内 IPv6 目标不会命中「国内直连」，而是落到 `final` 走代理。想按国家分流 IPv6，得自己加运营商的 `ip6-cidr` 地址段（比如 `2408::/16`、`2409::/16` 这些），维护成本不低。

另外，节点本身得支持 IPv6 才能访问 IPv6-only 站点——这是机场侧的事，配置改不了。

### 分流层的三个来源（为什么要三份）

主责的 blackmatrix7 `AdvertisingLite.list` **最后一次提交是 2025-12-21**——这份「Lite」构件其实已经被上游放了 10 个月（同仓库的全量版仍在每周重建，但 24 万条 / 12.5MB 太重）。所以现在分三层：

| 源 | 作用 | 更新频率 |
|---|---|---|
| blackmatrix7 `AdvertisingLite`（38,066 条） | 基线，已知广告主 | 冻结（2025-12） |
| 217heidai `adblockqxlite`（8,480 条） | 通用广告域名补充 | 每 8 小时 |
| AWAvenue `-Only.Ads`（665 条） | 广告 SDK / 联盟端点 | 约 2 周 |

三者去重后约 46,000 条，仍比黑猫全量轻一个数量级。**停任何一层都不至于全瘫**，这是刻意的冗余设计。

### 分流层的第三次调整（2026-11-03）

把 7 条 ConnersHua 的 Surge 规则集换成了 blackmatrix7 的 QX 原生列表，**tag 与 force-policy 全部保持不变**，因此 12 个策略组照旧有规则投喂、任何一条都能单独回滚。

| 策略组 | 旧（ConnersHua） | 新（blackmatrix7） | 规则数 |
|---|---|---|---|
| 海外抖音 | TikTok.list | TikTok.list | 35 |
| 电报代理 | Telegram.list | Telegram.list | 40 |
| 国际媒体 | !CN.list | GlobalMedia.list | 2341 |
| 港台番剧 | CN.list | BiliBili.list | 127 |
| 全球加速 | Proxy.list | Proxy.list | 7502 |
| 苹果服务 | Apple.list | Apple.list | 1880 |
| 国内网站 | Direct.list | Direct.list | 232 |

**保留在 ConnersHua 的 4 条**：`Direct+`（直连修正表，语义是"优先直连"而非分类，无等价物）、`Tracking`（黑猫的等价表是 EasyPrivacy 派生，39,936 条，口径完全不同）、`AI`（黑猫拆成 4 个文件且更旧）、`WeChat`（黑猫版丢掉了 17 条腾讯云新加坡 IP，海外语音可能变慢）。

**结果**：依赖 `opt-parser` 的条目从 12 降到 **5**（4 条 ConnersHua + AWAvenue），解析器仍然保留但不再是"全站分流单点"——它挂掉时损失的是这 5 条，而不是全部。

**已知行为变化**（换了之后要留意）：
- `国际媒体` 242 → 2,341 条，新增 918 条 `ip-cidr`。这些是按流媒体 CDN 汇总的 IP，**可能命中部分国内 CDN**，导致国内视频被送进国际媒体组。
- `苹果服务` 20 → 1,880 条，含 `apple.com.cn`/`icloud.com.cn`；若该组首选项是境外节点，国区 iCloud 会绕出国。
- `全球加速` 513 → 7,502 条，出口归属变化较大。
- `港台番剧` 由 `CN.list` 换成 `BiliBili.list`，语义收窄——爱奇艺台湾站、芒果国际不再命中这个组。

### 第四次调整：彻底摘掉 ConnersHua（2026-11-03）

`opt-parser` 从 5 条降到 **1 条**（只剩 AWAvenue 是 Surge 语法，无法回避）。最后 4 条 ConnersHua 的处理：

| 原条目 | 处理 | 代价 |
|---|---|---|
| `Direct+`（21 条） | 21 条规则**搬进 `[filter_local]`**，保持 direct | 依赖「本地规则先于远程匹配」，该顺序未实测 |
| `Tracking`（90 条，2026-06） | ~~换成黑猫 `Privacy.list`~~ → **已停用**（误伤面过大，见下） | 覆盖面大 444 倍且更旧，EasyPrivacy 含大量埋点/CDN，误杀面明显变大 |
| `AI`（74 条，2026-07） | 换成黑猫 4 个列表（102 条，**2025-06**） | **更旧**，且 1 条变 4 条 |
| `WeChat`（18 条含 17 个 IP） | 换成黑猫 `WeChat.list`（43 条纯域名，2025-06） | 丢掉腾讯云新加坡段 17 个 IP，海外语音/视频可能变慢 |

**2026-11-03 复盘后停用了 `隐私保护`**：39,936 条 EasyPrivacy 派生覆盖埋点/归因/OAuth 回调，故障频率约周级，且它比原来的 90 条**更旧**——收益/风险比是整套配置里最差的一处。

**这是用新鲜度换解析器独立性**——Tracking 和 AI 两条换的是更旧的源，不是更优的源。四条旧行都在配置里注释保留，回滚只需去掉行首 `; `。

**现状**：`[filter_remote]` **16 条**生效（停用 Privacy 后），其中只有 AWAvenue 依赖 `resource_parser_url`；规则总量约 **5.9 万条**（AdvertisingLite 38,066 占大头）。

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
| 保留 `fallback_udp_policy = reject` | **比 `udp_drop_list` 更激进**：所有未被规则命中的 UDP 一律拒绝。语音/游戏 App 出现连不上时，先查这一条，再查 `udp_drop_list` |
| 停用 ConnersHua 的 `Advertising.list` | 976 条规则；已由 blackmatrix7 的 Lite 版（38,066 条）取代，两套重叠没必要都跑 |
| 停用「神机去广告」 | 111 个 MITM 域名里 98 个（88.3%）已被黑猫声明，113 条规则里 83 条重复；且 2023-11 停更 |
| 移除「油管去 Shorts」 | 该模块只是隐藏 YouTube 的 Shorts 入口，本身不拦广告；模块数从 4 降到 3 |
| 移除「贴吧去广告」 | 只有 2 条规则 + 2 个脚本（含 97KB 的 protobuf 解析），上游 2026-03 停更；贴吧的开屏与 feed 广告已由黑猫 + 墨鱼覆盖，放弃的是列表页/信息流原生广告 |

## 一个容易误判的地方

`ddgksf2013.top` 的模块地址**会按请求头做内容协商**：用浏览器（或 `Accept: text/html`）打开会被 302 重定向到站点首页，看起来像死链；QX 以 `text/plain` 请求时才返回真正的配置。

判断这类地址是否有效，别用浏览器点开看，改用：

```bash
# 关键是 UA，不是 Accept 头（只发 Accept 会被 302 到首页）
curl -H "User-Agent: Quantumult%20X/1.5.0" https://ddgksf2013.top/rewrite/StartUpAds.conf | head
```

返回以 `// ==UserScript==` 开头才是配置本体。

## 常见问题

**导入后什么都没拦住**
先确认 `resource_parser_url` 那一行在不在、解析器能不能下载。所有 Surge 格式的列表都靠它转换，缺了它等于没规则。

**地区策略组显示为空**
见上面「节点命名」一节，改 `[policy]` 里的正则，或直接用「黑白名单」组。

**广告还漏**
广告拦截现在由 blackmatrix7 的 `AdvertisingLite.list` 主责（配置里 `tag=广告拦截Lite` 那一行）；ConnersHua 的 `Advertising.list` 已注释停用，只是留行备查。要换更勤的源，改 `tag=广告拦截Lite` 那一行即可。ConnersHua 仓库本身仍在维护（最后提交 2026-08-01），但其中 `CN.list` 与 `Apple.list` 停在 2023-11-11，已换成黑猫的 QX 原生表。

**看视频变卡 / 某些站点打不开**
多半是 `udp_drop_list = 443`，注释掉它试试。

**规则不更新**
先换镜像（把 `ghproxy.net` 换成 `gh-proxy.com` 或 `ghfast.top`），国内拉取失败基本都是网络问题。

**广告拦截用的是哪套**
现在是 blackmatrix7 的 `AdvertisingLite.list`（38,066 条）主责。ConnersHua 的 `Advertising.list`（976 条）已被注释停用，配置里留着一行备查——两者内容大量重叠，没必要同时跑。

隐私类域名仍由 ConnersHua 的 `Tracking.list`（90 条）负责，不影响。

**开启后 MitM 域名很多**
黑猫 582 个 + 墨鱼 437 个，**去重后约 726 个域名**会被解密（重复声明在 QX 里会归一，不会重复解密）。如果某个 App 白屏或加载卡住，按这个顺序关掉再试：黑猫去广告 → 墨鱼去开屏。另有番茄小说这个专项模块，带 8 个域名。

**番茄小说那两条规则要都加吗**
要，而且必须分别放对位置：`.snippet` 是 **29 条域名 / IP 拦截规则**，属于分流层，进 `[filter_remote]`；`.qxrewrite` 是 **16 条接口改写 + 11 个 MITM 域名**，属于重写层，进 `[rewrite_remote]`。两者内容不重复，只加一个会漏掉另一半。

> 小瑕疵一：`.snippet` 里有 3 条写成 `host, *xxx.com` 的规则，QX 的 `host` 不支持通配符，这几条大概率不生效。作者原文件如此，我没有改；想修就把 `host` 换成 `host-suffix`。
>
> 小瑕疵二：`.qxrewrite` 自带的 `hostname` 行有 5 条在 QX 里无效（`*.pstatp.com.*` 这类双通配不成立、`*.pangolin-sdk-toutiao` 少了 `.com`），还漏了 `*.byteimg.com` 和 `*.snssdk.com`。我已按它 16 条规则的实际匹配对象重新推导出 8 个域名，补在本仓库配置的 `[mitm]` 里，不需要你手动改上游文件。

**导入时提示「配置文件缺少模块 [server_local]」**
QX 会校验配置文件里各段落是否齐全，**空段落也必须存在**。本配置已包含 `[server_local]` 与 `[http_backend]` 两个空段落（只有注释，没有生效行），精简配置时别把它们删掉。

**IPv6 站点打不开**
先看节点是否支持 IPv6（机场的事）。配置层面已就绪：`[dns]` 有 IPv6 上游、AAAA 未禁用、本地 IPv6 段走直连。但注意 `geoip, cn` 只对 IPv4 生效，国内 IPv6 目标会走代理而不是直连。

**为什么 `苹果服务` 组的 `direct` 排第一**
QX 的 `static` 组按顺序取第一个可用项。原来 `direct` 在最后，等于 App Store 下载、iCloud 照片同步、系统更新**全部走香港节点**——下载变慢、节点流量翻倍。现在 `direct` 提到首位，只有直连不可用时才回落节点。

**MITM 名单里有银行和政务域名，会不会出问题**
会。黑猫那份重写模块的 582 个 MITM 域名里包含 12 个银行/券商（`mbs.boc.cn`、`creditcardapp.bankcomm.cn`、`m.stock.pingan.com`、`v.icbc.com.cn`、`lban.spdb.com.cn` 等）、3 个政务民生（`fuwu.nhsa.gov.cn` 国家医保、`gab.122.gov.cn` 交管12123、`chaoxing.com`），以及微信/淘宝/京东/拼多多/小红书的主链路域名。
这些域名一旦启用证书 pinning，MITM 就会失败，表现为**「微信里打开某个医保/银行页面白屏」而其他一切正常**——极难归因到代理。
遇到这类白屏，先按顺序注释：`黑猫去广告` → `墨鱼去开屏`。另外该名单里还有 19 条中缀通配写法（`ad*.sina.com` 这类）在 QX 里无效，2 个裸 IP 也无效。

## 来源与致谢

- [Orz-3/QuantumultX](https://github.com/Orz-3/QuantumultX) — 配置模板
- [ConnersHua/RuleGo](https://github.com/ConnersHua/RuleGo) — 分流规则集（其去广告模块已停用）
- [zqzess/rule_for_quantumultX](https://github.com/zqzess/rule_for_quantumultX) — 番茄小说去广告（分流 + 重写两份）
- [217heidai/adblockfilters](https://github.com/217heidai/adblockfilters) — 通用广告域名补充列表
- [TG-Twilight/AWAvenue-Ads-Rule](https://github.com/TG-Twilight/AWAvenue-Ads-Rule) — 广告 SDK 端点列表
- [fmz200/wool_scripts](https://github.com/fmz200/wool_scripts) — App 专项去广告重写（当前启用字节系、小红书）
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
