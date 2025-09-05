---
layout:     post
title:      新手也能挖：图形化矿工 MultiMiner 中文完全指南
date:       2025-09-05
header-img: img/post-bg-desk.jpg
catalog: true
---

MultiMiner 是一款跨平台的**图形化挖矿客户端**，专为新手与进阶玩家设计，支持 Windows、macOS 及 Linux。无需深度命令行知识即可在 **GPU、ASIC、FPGA** 等多硬件间自由切换币种，核心调用高效 `bfgminer` 底层引擎，带来一键式挖矿体验。

---

## 功能亮点速览

- 设备自动扫描：实时识别可用 GPU、ASIC、FPGA  
- 硬币多选：内置比特币、莱特币、狗狗币等主流币  
- 盈利策略：定期抓取 **CoinChoose** 数据，优先挖高收益币  
- 远程监控：对接 **MobileMiner API**，手机也能看算力  
- 智能保活：崩溃后自动重启进程，7×24 在线  
- 挖矿线程：根据电脑闲置时间动态调整强度  

---

## 安装不求人：三步到位

### Windows 懒人包

1. 前往官方 **GitHub Releases** 下载最新 `.exe` 安装包  
2. 双击运行即可（无需管理员权限）  
3. 完成后启动桌面快捷方式 → 打开即挖  

👉 [想在十分钟内让显卡跑起来？点击查看秒装教程](https://okxdog.com/)

### macOS & OS X 全流程

1. 率先安装 **[XQuartz]**  
2. 安装最新 **[Mono]**  
3. 下载 `.app.zip`，解压后双击 `MultiMiner.app`

MultiMiner 会自动拉取兼容版 `bfgminer`，省去终端折腾。

### Debian 系列 Linux 指南

```bash
sudo apt-get install mono-complete
sudo add-apt-repository ppa:unit3/bfgminer
sudo apt-get update
sudo apt-get install bfgminer
# 解压后运行
mono MultiMiner.Win.exe
```

---

## 配置硬币与矿池：快速上手

1. 首次启动后启动 **Getting Started 向导**  
2. 按提示选引擎 → 选币种 → 填矿池信息  
3. 打开 **Configure Coins** 添加多条备用池，开启负载均衡  
4. 选中 “自动选择盈利币” → 设置刷新周期（建议 30–60 min）

## MobileMiner：手机监控新姿势

- 在 **Configure Settings** 中输入邮箱与 Application Key  
- 无需开端口映射，支持外网穿透  
- 可远程重启、切币、查看算力曲线  

👇 [立即体验手机看矿机，设置仅需60秒](https://okxdog.com/)

---

## 示例：30 行代码启动 API 监控

以下示例展示如何通过 `MultiMiner.Xgminer` DLL 快速读取设备算力：

```csharp
// 引用 MultiMiner.Xgminer.dll 与 MultiMiner.Xgminer.Api.dll
string exePath = "./bfgminer";
Xgminer.Installer.InstallMiner(exePath);

MinerConfiguration cfg = new MinerConfiguration();
cfg.ExecutablePath = exePath + "/bfgminer.exe";
cfg.ApiListen = true;
cfg.ApiPort = 4028;

List<Device> devs = new Miner(cfg).ListDevices();
if (devs.Count > 0)
{
    cfg.Pools.Add(new MiningPool() { Host = "mint.bitminter.com", Port = 3333, Username = "demo", Password = "demo" });
    Process p = new Miner(cfg).Launch();
    var api = new Xgminer.Api.ApiContext(4028);
    for (int i = 0; i < 6; i++)
    {
        Thread.Sleep(10_000);
        var info = api.GetDeviceInformation();
        foreach (var d in info)
            Console.WriteLine($"#{d.Index}: {d.CurrentHashrate} / {d.AverageHashrate} H/s");
    }
    api.QuitMining();
    p.Close();
}
```

---

## FAQ：新手高频问题一图清

**Q1：MultiMiner 只能用 bfgminer 吗？**  
A：目前官方仅对接 bfgminer，但 `MultiMiner.Engine` 提供了无 UI 接口，理论上可拓展其他引擎。

**Q2：为什么我的 FPGA 不被识别？**  
A：绝大多数情况需额外安装设备驱动，详见后文驱动列表。

**Q3：Windows 打不开窗口？**  
A：检查是否缺少 .NET 4.5 以上组件，或使用 zip 版本直接运行 `MultiMiner.Win.exe`。

**Q4：如何彻底卸载？**  
A：删除程序文件夹即可；无注册表残留，简单干净。

**Q5：能否自动切换到低功耗模式？**  
A：在 **Advanced Settings** 启用「根据 CPU 空闲率调整强度」，系统自动降功耗。

---

## 驱动与固件对照表

| 矿机类型 | 所需驱动页面 | 备注 |
|---|---|---|
| Block Erupter | SILabs CP210x | USB 桥接驱动 |
| Blue / Red Fury | 社区整合包 | Windows 专用 |
| BFL / BitForce | FTDI VCP | 通用串口驱动 |
| HashBuster Micro | Zadig WinUSB | USB 驱动替代品 |
| Bi·Fury | 波兰官方站点 | 安装前务必阅读 |
| AMD GPU | AMD 官网 | 建议 Pro 驱动 |

注：除官网外请勿随意下载第三方驱动，确保哈希安全。

---

## 开源与社区支持

- 官方论坛：[talk.multiminerapp.com](talk.multiminerapp.com)  
- 代码仓库：GitHub `nwoolls/MultiMiner`  
- 许可：MIT，可自由再发布、修改、商用  

项目核心由以下模块构成，方便二次开发：

- **MultiMiner.Xgminer**：控制 bfgminer 进程  
- **MultiMiner.MobileMiner.Api**：REST 远程监控  
- **MultiMiner.Coinchoose.Api**：实时收益列表  
- **MultiMiner.Engine**：无 UI 引擎，便于自建前端  

---

## 捐赠硬件，让开发者也能测真机

开发者 **Nathanial Woolls** 并不寻求 BTC / LTC，而是欢迎用户捐献闲置 **FPGA、USB 矿机、旧 GPU**。更多真实硬件可复现 Bug、提升兼容性，也是回馈社区的最佳方式。若设备不便寄送，详尽的错误日志同样珍贵。

祝你挖矿畅快，算力长虹！