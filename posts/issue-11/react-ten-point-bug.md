# 深度解析：React 满分核弹级漏洞 (React2Shell) 技术分析

> **警报级别**：🔴 **CVSS 10.0 (Critical / 最高危)**
> **漏洞编号**：CVE-2025-55182 (React) / CVE-2025-66478 (Next.js)
> **漏洞类型**：CWE-502（不可信数据的反序列化）
> **影响范围**：
>
> - React 19.0.0, 19.1.0, 19.1.1, 19.2.0
> - 受影响的包：`react-server-dom-webpack`、`react-server-dom-parcel`、`react-server-dom-turbopack`
> - Next.js 15.x / 16.x，以及 Next.js 14.3.0-canary.77 及之后的 canary 版本（使用 App Router 时）
> - 所有使用 React Server Components (RSC) 的框架

根据“[技术爬爬虾](https://www.youtube.com/watch?v=bMiOYsDtpGM&list=TLGG3iTaldW4GG0xNDEyMjAyNQ)”与“[原子能](https://www.youtube.com/watch?v=LSiYdiMGS4U&list=TLGGeKSNyGPDaRAxNDEyMjAyNQ)”的深度解读，本文将剖析这一被称为 **"React2Shell"** 的严重漏洞，揭示攻击者如何利用 React 的底层协议攻破服务器。

## 一、 核心背景：RSC 与 Flight 协议

要理解这个漏洞，首先必须理解 React Server Components (RSC) 的通信基石——**Flight 协议**。

在 Next.js 等现代框架中，React 不再只是前端库，而是演变为全栈架构。服务器（Server）与客户端（Client）之间通过 Flight 协议通信，该协议将组件树序列化为文本流。

- **数据块 (Chunks)**：Flight 协议将数据序列化为一行行文本，通过 HTTP POST 发送。
- **特殊标记**：解析器依赖特殊字符来识别数据类型：
  - `$Q`：代表 Map 集合
  - `$@`：代表 Promise（异步任务）
  - `$B`：代表 Blob（二进制大对象）
  - `$`：用于引用其他数据块（如 `$1` 引用 ID 为 1 的块）

这种机制允许复杂对象（如 Promise）在网络间传输，但也埋下了“反序列化漏洞”的种子。

## 二、 漏洞根源：盲目的信任

漏洞的核心在于 **React 解析器（Parser）的盲目信任**。

当服务器接收到客户端发来的 Flight 协议数据时，React 的内部函数（如 `parseModelString`）会执行递归解析。**致命错误在于：服务器默认认为接收到的数据结构是安全的，未对反序列化过程中的对象类型和属性访问做严格限制。**

## 三、 攻击链条：从伪造数据到 RCE

攻击者无需任何身份验证，只需三个步骤即可接管服务器：

### 第一步：欺骗解析器（伪造 AST）

攻击者构造一个恶意的 JSON Payload，伪装成合法的 Flight 数据流。利用 `$@` 标记，攻击者可以伪造一个处于 `resolved` 状态的 Promise 对象，强制服务器跳过等待，直接进入后续处理流程。

### 第二步：原型链攀升与污染

这是攻击最关键的一环。利用 Flight 协议的“引用机制”，攻击者可以访问对象的深层属性。

- 由于缺乏 `hasOwnProperty` 检查，解析器允许通过引用遍历到对象的原型 `__proto__`。
- 攻击者顺藤摸瓜找到 `constructor`（构造函数），最终获取到 JavaScript 中最危险的工具——**Function 构造器**。

> **风险点**：拥有 `Function` 构造器意味着你可以通过字符串（String）直接创建并运行任意 JavaScript 代码。

### 第三步：Payload 注入与触发执行 (The "Thenable" Trick)

拿到执行权限后，攻击者需要通过巧妙的方式触发代码：

1. **注入恶意指令**：利用 `$B` (Blob) 或其他标记的处理逻辑，将恶意代码（如 `require('child_process').exec(...)`）注入到对象的属性中。
2. **触发执行**：利用 JavaScript 的 `await` 特性。攻击者在 Payload 中构造一个带有 `then` 方法的对象（Thenable Object）。
3. **自动化攻击**：当服务器解析器尝试“await”这个伪造对象时，会自动执行 `then` 方法中的恶意构造流程，从而在服务器端执行任意代码。

## 四、 漏洞后果

一旦攻击成功，后果是灾难性的：

- **任意命令执行 (RCE)**：攻击者可调用 Node.js 的 `child_process` 执行系统命令（如 `rm -rf /`，安装挖矿木马等）。
- **数据泄露**：完全读取服务器上的环境变量（AWS Keys, 数据库密码）、源代码和用户数据。
- **无痕入侵**：攻击仅需一个 HTTP POST 请求，无需登录，且容易绕过传统的 WAF 防御。
- **供应链风险**：攻击者可窃取 Git 凭据和云环境配置，导致更大范围的供应链攻击。
- **影响范围极广**：React 被 Facebook、Instagram、Netflix 等大型平台广泛使用，潜在影响面巨大。

## 五、 修复方案

React 团队已发布紧急补丁。

**已修复版本**：

- **React**: 19.0.1, 19.1.2, 19.2.1
- **Next.js**: 15.0.5, 15.1.9, 15.2.6, 15.3.6, 15.4.8, 15.5.7, 16.0.7

**修复的核心逻辑**：
官方补丁在解析器引用对象属性时，强制增加了 `hasOwnProperty` 检查。
这就像给房子加了一道安检门：解析器现在**只能读取对象自身拥有的属性**，严厉禁止访问 `__proto__` 和 `constructor`。这一改动直接切断了攻击者攀升原型链获取 `Function` 构造器的路径。

**临时缓解措施**（无法立即更新时）：

- 禁用或限制 RSC 端点的访问
- 应用 Web 应用防火墙（WAF）规则，阻止包含特定路径标记的多部分请求
- 加强对服务器日志的监控，注意异常的 HTTP POST 请求
- **AWS 用户**：可启用 `AWSManagedRulesKnownBadInputsRuleSet`（v1.24+ 已包含此漏洞的防护规则），或部署自定义 WAF 规则检测 `next-action` / `rsc-action-id` 请求头与 `"status":"resolved_model"` 请求体的组合（详见 [AWS 安全公告](https://aws.amazon.com/security/security-bulletins/rss/aws-2025-030/)）

---

## 六、 已知攻击活动

根据安全研究机构报告，该漏洞在公开披露后**数小时内**就已被积极利用：

- **国家级黑客组织**：包括 Earth Lamia、Jackpot Panda 等组织，针对金融、零售、物流、IT、教育和政府等全球多个行业发起攻击，试图建立持久后门进行网络间谍活动。
- **恶意软件部署**：攻击者利用该漏洞部署了多种恶意软件，包括 Mirai/Gafgyt 变种、加密货币挖矿程序和 RondoDox 僵尸网络。
- **凭据窃取**：攻击者尝试窃取 Git 和云环境的凭据，可能导致云基础设施被入侵和供应链攻击。

---

**建议操作：**
如果您正在维护使用 Next.js App Router 或 React 19 的项目，请立即执行以下命令检查并升级：

```bash
npm audit
# 或直接升级到最新版本
npm install next@latest react@latest react-dom@latest
```

**漏洞检测工具**：
可以使用专门的扫描工具检测您的应用程序是否存在该漏洞，例如访问 [cve-2025-55182.com](https://cve-2025-55182.com/) 提供的检测器。
