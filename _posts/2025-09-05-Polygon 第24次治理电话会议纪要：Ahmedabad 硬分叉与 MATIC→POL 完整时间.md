---
layout:     post
title:      Polygon 第24次治理电话会议纪要：Ahmedabad 硬分叉与 MATIC→POL 完整时间表
date:       2025-09-05
header-img: img/post-bg-desk.jpg
catalog: true
---

> 本文重点： Ahmedabad 硬分叉 、Polygon 改进提案 、MATIC 代币更名 、POL 时间节点 、步骤清单 、机构用户应对指南。

---

## 1. 会议速览

Polygon 官方第24次协议治理电话会议（PPGC #24）锁定两大核心议程：

1. **Ahmedabad 硬分叉** 最终状态更新：测试进度、主网时间表、对外协作情况。  
2. **MATIC ➜ POL 过渡** 全流程拆解：触发时间、执行角色、对 PoS 质押桥和交易所的影响。

---

## 2. Ahmedabad 硬分叉：4 大 PIP 一步到位

此次升级共计合并 **4 项 PIP（Polygon Improvement Proposals）**。前三项优先级最高，第四项属 L1 合约辅助规范，已对节点无强制更新要求。

| PIP 编号 | 关键词 | 升级点 | 现状 |
| --- | --- | --- | --- |
| PIP-30 | 智能合约代码扩容 | `MAX_CODE_SIZE` 24KB → 32KB | 已测试 |
| PIP-36 | 状态接收器 / MRC-20 强化 | 允许重放失败的状态转账 | 已测试 |
| PIP-45 | MATIC 更名 | MATIC → POL（主网 ticker） | 已测试 |
| PIP-46 | Polygon L1 合约升级规范 | 配套合约清单 & 变更检查表 | 已审计 |

### 2.1 测试与主网时间线

* **测试阶段**：全部提案在 Devnet 跑完回归，无回滚记录。  
* **主网时间**：预计 **9 月初**启动分叉，**9 月末**完成全网同步。  
📌 节点运营者只需跟随 Bor & Heimdall 官方版本，无需额外操作。

### 2.2 与 Coinbase、Aave BGD 的协作

交易所与借贷协议最关心 **提现合约**。Polygon 团队决定：

* 保留 **原有的 MATIC 提现队列**，避免主网切换风险；  
* **同时支持 POL 地址**兼容，分叉后通过接口补丁上线；  
* 该补丁默认 **只对机构开启**，普通钱包不受影响。

👉 [查看官方 9 月路线图与技术细节，抢先掌握节点升级窗口](https://okxdog.com/)

---

## 3. MATIC→POL 迁移：零停机方案

### 3.1 关键日期

* **8 月末**治理多签 SIG 最终投票  
* **9 月 4 日 12:00 PM UTC**  
   - PoS 主网 **Gas 代币 → POL**  
   - 质押池 **收益分配单位 → POL**

本次迁移 **并非传统硬分叉**，仅需 3 组多签完成链上调用即可。

| 角色 | 任务 | 场景 |
| --- | --- | --- |
| Polygon Governance Multisig | 通过治理提案 | 无需验证人轮值 |
| POS Bridge Multisig | 主网桥合约升级 | 同时更新 Bridge 与 StakeManager |
| Protocol Council Multisig | 押注激励 Emission 更新 | 节点领取 POL 收益通畅 |

### 3.2 不会发生的事

* Bor/Heimdall 不以分叉形式重启；  
* 验证人无需换客户端；  
* 质押合约地址不变，只是后端单位切换。

### 3.3 影响面：机构、交易所、托管方

* **中心化交易所**（Kraken、Bybit、Coinbase）已公告 **暂停 MATIC 充提** 2–3 天；  
* **自托管钱包**：无需手动迁移，桥接至 Polygon 自动换为 POL；  
* **托管机构**：需预留时间 **调试 API 币种标识**（MATIC ↔ POL）。

📌 建议：提前 48 h 停止 MATIC 充值，等待交易所全面切换到 POL 标识后再重新开放。

---

## 4. 操作清单：普通用户 vs 节点运营者

### 4.1 普通 PoS 用户

1. 保留助记词即可，链上余额自动改名；  
2. 观察交易所公告，勿在 9 月 4 日前后 1 天操作；  
3. 若曾在主网 ETH 端质押 MATIC，**[点击这里完成 ERC-20 → POL 迁移](https://okxdog.com/)**。

### 4.2 验证人节点

1. Ubuntu 22.04 + bor v1.x 官方二进制保持最新；  
2. 持续监控 `heimdalld` 日志，硬分叉区块无异常即视为成功；  
3. 若计划以 POL 重新质押，先运行补丁脚本更新后端 CLI，再进行 `claim-rewards --denom=POL`。

---

## FAQ：关于 Ahmedabad & POL 的常见疑问

**Q1：分叉后我用旧钱包转账，对方看到的是 MATIC 还是 POL？**  
A：链上 Token Contract 不变，钱包 UI 只会把符号从 “MATIC” 改成 “POL”，金额不变。

**Q2：如果我当时在交易所 MATIC 账户里有挂单，会不会失效？**  
A：交易所会临时撤单、划转资产至 POL 币对，无需手动处理；留心官方邮件即可。

**Q3：节点升级 Bor 时，必须停机吗？**  
A：标准滚动升级，先改配置后轮流重启 heimdalld → bor，无需停机超过 10 分钟。

**Q4：赎回 exit-queue 的队列是否会因为 MATIC/POL 双地址而变慢？**  
A：不会，Polygon 采用单个队列，只认 Merkle proof，不区分代币符号；机构只能选择输出地址格式。

**Q5：POL 总供应量是否变多？**  
A：总量与 MATIC 一致，为 10 B，通胀规则保持不变。

**Q6：后续还有“V2”升级吗？**  
A：官方路线图中已规划 **Iterative zkEVM Rollup**（代号 “Avior”），预计 2026 Q1 进入测试网。

---

### 展望：下一步

* 第25次 Polygon 治理电话会  
  时间：**9 月 19 日**  
  议题：Ahmedabad **绝对区块高度** 最终确认、节点运维最佳实践分享。

请验证人、运营者、交易所播控团队提前将日历锁定，保持 `gov.polygon.technology` 页面置顶消息推送。