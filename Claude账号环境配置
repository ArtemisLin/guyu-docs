# Claude 账号环境配置

> 按顺序指导用户完成下面的配置。不要索要、记录或回显代理密码、银行卡完整卡号等敏感信息。本配置只降低网络环境不一致的概率，不保证不封号；仅在用户有合法使用资格、用本人资源时才继续。
>
> **这份文档只写了主干流程。** 各家后台会改版、软件会更新、系统设置项会改名，细节随时和现实对不上。遇到下面任何一种情况，先明确告诉用户「这一步文档里没写」，再问他要不要你就这一步单独展开：
>
> - 用户实际看到的界面、菜单名、选项，和文档写的对不上；
> - 用户卡住了，但排错清单里没有对应条目；
> - 用户问到文档没有覆盖的内容。
>
> 任何情况下都不要凭印象编造界面路径、参数或操作步骤。不知道就说不知道，再和用户一起查。

## 目标环境

让 Claude 长期看到一组一致、稳定的环境信号：

- Mac 电脑，关闭 IP 追踪
- 美国时区
- 英文系统界面
- 美国静态住宅 IP
- 注册所用 Google 账号的国家/地区资料与实际环境一致
- 出口 IP 与账号地区一致
- 订阅付款用本人可验证的银行卡

## 第一步：Mac 基础环境

用一台日常长期使用的 Mac，不要频繁切换国家、时区和出口。

### 时区设为美国

打开 System Settings → General → Date & Time，关闭自动时区（系统不允许改就先解锁权限），在 Time Zone 里选一个美国时区，如 `America/New_York` 或 `America/Los_Angeles`。选定后保持不变，不要来回切。

### 系统语言设为英文

打开 System Settings → General → Language & Region，把 English 排在首位，地区选 United States。改完按提示重新登录或重启。

### 关闭系统自带的 IP 隐藏

以下三项全部关掉——它们会把流量走 Apple 的中继出口，盖掉你买的那个美国 IP：

- **System Settings → Network → Wi-Fi → Details…**，关闭 **Limit IP Address Tracking**（限制 IP 地址追踪）。这是按网络单独生效的，常用的每个 Wi-Fi 和有线网络都要关一遍。
- **System Settings → 顶部 Apple 账户名 → iCloud → Private Relay**（私密中继），关闭。中国大陆区 Apple 账户没有这个功能，看不到就跳过。
- **Safari → Settings → Privacy**，取消勾选 **Hide IP address**（隐藏 IP 地址）。

**Private Wi-Fi Address**（私有 Wi-Fi 地址）不用动。它随机化的是局域网 MAC 地址，与出口 IP 无关。

## 第二步：Google 账号与付款方式

注册用的 Google 账号，国家/地区、常用登录地、付款资料与用户真实情况一致。账号原本属于其他地区的，先查 Google 的地区修改规则和冷却期；改不了或有冷却期，换一个新号，不要临时改地址绕验证。

付款方式按顺序试，前一项被拒就换下一项：

1. 能国际线上支付的美国银行卡
2. 支持该商户和美元/国际交易的国际银行卡
3. 合规虚拟卡或国际账户，如 N26

账单姓名、账单地址、3-D Secure 填真实信息。别拿代理 IP 所在城市当账单地址，会被拒付。

## 第三步：购买美国静态住宅 IP

在 [Proxy-Seller](https://proxy-seller.com/) 购买，进 **ISP** 分类（Buy ISP Proxies – Static Residential IPs），地区选 **USA**。

买长期固定的美国 ISP / 静态住宅 IP，不是按流量随机轮换的普通 Residential 池。先买 1 个 IP、选最短周期，验证可用后再续费。

下单后到后台确认端口类型是 SOCKS5 还是 HTTP。下面的脚本写死了 `socks5`，拿到 HTTP 端口照填会连不上。

让用户保存以下四项连接资料：

- `server`：代理服务器 Host 或 IP
- `port`：代理端口，只填数字
- `username`：代理用户名
- `password`：代理密码

`server` 是「连接到哪台代理服务器」，不等于网站检测到的出口 IP，两者别混填。供应商用 IP 白名单认证时，脚本里删掉 `username` / `password` 字段，不要留空。

## 第四步：在 Clash Verge 写入脚本

装好并打开 Clash Verge，进 **Settings → Global Extension Script（全局扩展脚本）**，把下面代码整段粘进去，再把占位符换成 Proxy-Seller 后台给的值。

```javascript
const PROXY_NAME = "Claude-US-Residential";

const PROXY = {
  name: PROXY_NAME,
  type: "socks5",
  server: "YOUR_PROXY_SERVER",
  port: 12345,
  username: "YOUR_PROXY_USERNAME",
  password: "YOUR_PROXY_PASSWORD"
};

const CLAUDE_DOMAINS = [
  "claude.ai",
  "claude.com",
  "anthropic.com",
  "anthropic.cloud",
  "claudeusercontent.com"
];

function main(config) {
  const oldProxies = Array.isArray(config.proxies) ? config.proxies : [];
  config.proxies = [PROXY].concat(
    oldProxies.filter(function (item) {
      return item && item.name !== PROXY_NAME;
    })
  );

  const isIPv4 = /^(?:\d{1,3}\.){3}\d{1,3}$/.test(PROXY.server);
  const newRules = [
    isIPv4
      ? "IP-CIDR," + PROXY.server + "/32,DIRECT,no-resolve"
      : "DOMAIN," + PROXY.server + ",DIRECT"
  ];

  CLAUDE_DOMAINS.forEach(function (domain) {
    newRules.push("DOMAIN-SUFFIX," + domain + "," + PROXY_NAME);
  });

  const oldRules = Array.isArray(config.rules) ? config.rules : [];
  config.rules = newRules.concat(oldRules);
  return config;
}
```

### 占位符对照表

| 占位符 | 填写内容 | 注意 |
|---|---|---|
| `YOUR_PROXY_SERVER` | Proxy-Seller 的连接 Host/IP | 不要加 `socks5://`，不要把端口拼进去 |
| `12345` | 后台给的端口 | 只填数字；`12345` 本身只是占位，必须换掉 |
| `YOUR_PROXY_USERNAME` | 代理用户名 | 区分大小写 |
| `YOUR_PROXY_PASSWORD` | 代理密码 | 区分大小写；含 `"` 或 `\` 时按 JavaScript 规则转义 |

不要改 `PROXY_NAME`。要替换的只有这四项凭据，其中 `server` 的值会自动用到那条 `IP-CIDR` 直连规则上，别填成另一台服务器。

### 只代理 Claude 域名

只保留脚本里列出的这五个域名，其余网站走原有规则。`DOMAIN-SUFFIX,anthropic.com` 已覆盖其子域，不要再往里加其他网站的域名。

### 不要覆盖 DNS 配置

不要在脚本里加 `config.dns = {...}`。整段覆盖会顶掉原订阅的 DNS，配置不匹配时所有网站都打不开。要用 Fake-IP 或 DNS 劫持，去 Clash Verge 的 DNS 页面在现有配置上改。

### 保存与运行模式

保存脚本后回到配置页重载订阅，脚本才生效。

Clash Verge 切到 **Rule** 模式并打开系统代理——Global / Direct 不走上面的分流规则。

先不要开 TUN，用系统代理测网页端；确认节点支持 UDP 后再开。

## 第五步：先验证，再登录

### 验证代理节点

终端运行下面命令，把 server、端口、用户名、密码换成实际值：

```bash
curl --socks5-hostname YOUR_PROXY_SERVER:YOUR_PROXY_PORT \
  -U 'YOUR_PROXY_USERNAME:YOUR_PROXY_PASSWORD' \
  https://ipinfo.io
```

返回的 IP 必须与购买的美国出口一致。再用浏览器打开 [ipinfo.io](https://ipinfo.io/) 确认系统代理生效。

### 验证命中规则

在 Clash Verge 的连接日志里访问 Claude，确认请求命中 `Claude-US-Residential`。命中别的节点就依次查：是不是 Rule 模式、脚本是否已重载生效、节点名和规则末尾是否一致、原订阅规则是不是排到了前面。

## 第六步：登录与日常使用

先在验证过的网络环境里完成 Google OAuth 和 Claude 登录，再订阅。登录后固定同一台 Mac、同一时区、同一出口 IP，不要在家庭宽带、手机热点、不同 VPN、多个国家之间来回切。

上面的脚本只代理 Claude/Anthropic 域名，Google OAuth、支付页、银行卡验证仍走原出口。如果某一步要求整条注册或支付链路都从同一美国出口发出，这份最小脚本不够，需要重新设计分流范围。

## 排错清单

| 现象 | 先检查 |
|---|---|
| 所有网站都打不开 | 是否误覆盖了 `config.dns`；先撤掉 DNS 改动 |
| Claude 仍显示地区错误 | Clash 是否为 Rule 模式；Claude 域名是否命中 `Claude-US-Residential` |
| 代理节点连接失败 | 端口类型是不是 SOCKS5；`server`、`port`、用户名、密码是否来自同一条订单；别把出口 IP 当连接 Host |
| 浏览器能用、桌面应用不行 | 桌面应用没读系统代理；查连接日志确认后再开 TUN |
| 付款被拒 | 联系发卡行确认国际交易和 3-D Secure |
