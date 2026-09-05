# WARP MASQUE 配置生成器

一键生成 Cloudflare WARP 的 mihomo 配置，41 个节点，跑在 GitHub Actions 上。
不用自己装环境，不用服务器。

## 怎么用

**1. Fork 这个仓库**

点右上角 Fork。

**2. 打开 Actions 页面**

Fork 过来的仓库默认不开 Actions，会看到一个提示，点
`I understand my workflows, go ahead and enable them` 就行。

**3. 跑一次**

左边选 `生成 WARP MASQUE 配置`，右边点 `Run workflow`，绿色按钮再点一次。
等一分钟左右。

**4. 下载**

跑完点进这次运行的页面，最下面 `Artifacts` 里有个 `warp-masque-config`，
下载解压。

**5. 导入客户端**

解压出来的 `warp-masque.yaml` 就是配置，直接导入 mihomo 内核的客户端。

## 必须用 mihomo Alpha 内核

masque 只有 mihomo 的 Alpha 分支才有，稳定版导进去会直接报错说不认识这个类型。
所以客户端不光要是 mihomo 内核，还得能切到 Alpha。

**Windows / macOS / Linux**

[Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev) —— 装完打开
`设置 → Clash 内核`，把内核换成 Alpha，等它下载完自动重启。

> 切内核和升级 Verge 本身是两回事。把程序更新到最新版**不会**让内核变成 Alpha，
> 得手动在这个页面切。没切就导入会报 `unsupport proxy type: masque`。

**Android**

[ClashMetaForAndroid](https://github.com/MetaCubeX/ClashMetaForAndroid/releases/tag/Prerelease-alpha)
—— 认准 `Prerelease-alpha` 那个 tag，正式版不行。

**iOS**

[ClashMi](https://github.com/KaringX/clashmi) —— 内置 mihomo 内核，
把 `warp-masque.yaml` 直接导进去就行。同一个 App 也有 macOS / Android /
Windows / Linux 版本。

Shadowrocket 也能用，但它不吃 yaml，要把 artifact 里的 `usque-config.json`
转成 `masque://` 链接再导入。有人做了个纯前端的转换器：
<https://github.com/KxK3lv1n/usque-shadowrocket-clashmi-converter>

**跨平台，也可以看看**

[FlClash](https://github.com/chen08209/FlClash) —— 界面比较新，Windows / macOS /
Linux / Android 都有。内核版本在设置里换。

**不用图形界面**

直接下 [mihomo Alpha](https://github.com/MetaCubeX/mihomo/releases/tag/Prerelease-Alpha)
二进制，`mihomo -d 配置目录` 跑起来就行。

### 这些用不了

Surge、Quantumult X、Karing 不是 mihomo 内核，也不认 masque，导进去没用。

## 关于节点

41 个节点是同一个 WARP 账号的不同接入地址，**出口 IP 是一样的**。
多节点是为了某个地址被墙时能自动换一个，不是多国家落地。
想选国家得用 WARP+ 或 ZeroTrust，这个仓库不支持。

里面有 20 个 IPv6 节点，你没 IPv6 的话它们会连不上，但客户端会自动跳过，
不影响用。

## 几个提醒

配置里的 `private-key` 相当于账号密码，别往外发。artifact 默认存 7 天，
公开仓库的 artifact 谁都能下载，介意就把 fork 出来的仓库设成私有。

想换一套密钥就重新跑一次 workflow，每次都是全新账号。

别写成定时任务高频跑，WARP 会风控封号。

## 常见问题

### 导入报「unsupport proxy type: masque」

完整报错长这样：

```
订阅配置校验失败，请检查订阅配置文件，变更已撤销
level=error msg="proxy 0: unsupport proxy type: masque"
```

**内核还是稳定版，没切到 Alpha。** 这是目前最多人踩的一个。

Clash Verge Rev 的切法：打开「设置 → Clash 内核」，选 Alpha，点切换，
等它下载完会自动重启内核。然后再导入配置。

注意切内核和更新程序本身是两回事，把 Verge 升到最新版并不会让内核变成 Alpha。

### Actions 页面有个黄色警告，要紧吗

如果你看到的是这个：

```
Node.js 20 is deprecated. The following actions target Node.js 20 ...
```

不影响结果，配置照样能生成。这个仓库已经升级到 node24 的 action 版本，
重新 Fork 或者同步一下上游就没有了。

Fork 早了的话，把 `.github/workflows/warp-masque.yml` 里这两行改一下：

```yaml
uses: actions/checkout@v6
uses: actions/upload-artifact@v6
```

### 为什么节点延迟不一样，但测速结果都差不多

41 个节点是同一个 WARP 账号的不同接入地址，**出口 IP 是同一个**。
延迟差异来自你到接入点的网络路径，真正落地的还是那台 Cloudflare 机器。

所以挑延迟最低的用就行，不用一个个试速度。

### artifact 过期了怎么办

重新跑一次 workflow，会生成一套全新的密钥和配置。artifact 默认存 7 天，
想留久一点在 Run workflow 的时候把保留天数改大。

### iOS 怎么用

用 [ClashMi](https://github.com/KaringX/clashmi)，它内置 mihomo 内核，
`warp-masque.yaml` 直接导入就行，和桌面端一样。

Shadowrocket 也支持 masque，但它不认 yaml，得把 artifact 里的
`usque-config.json` 转成 `masque://` 链接。用这个在线转换器：
<https://kxk3lv1n.github.io/usque-shadowrocket-clashmi-converter>

Surge、Quantumult X、Karing 不行。

### 能选国家吗

不能。免费 WARP 账号的出口由 Cloudflare 任播决定，你在哪就近落哪。
要指定落地得用 WARP+ 或者 ZeroTrust，这个仓库不支持。

### 跑 workflow 报 login failed

```
Failed to connect tunnel: login failed!
```

账号被 Cloudflare 风控了，通常是短时间内建连太频繁导致的。
重新跑一次 workflow 拿新账号就行，另外别把 workflow 改成定时高频跑。

## 想改配置

`scripts/gen_masque.py` 顶部三个常量控制节点池：

```python
V4    = [...]   # IPv4 接入地址
V6    = [...]   # IPv6 接入地址
PORTS = (...)   # 端口
```

分流规则用的是 ACL4SSR，改 `RULESETS` 那个列表。
