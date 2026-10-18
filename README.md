# met-mandatory-verify-checker

[![License: MIT](https://img.shields.io/badge/Code%20License-MIT-yellow.svg)](LICENSE)
[![Content: ARR](https://img.shields.io/badge/Content%20License-ARR%20%7C%20citable-blue.svg)](LICENSE-CONTENT)
[![Status: RESERVED](https://img.shields.io/badge/status-RESERVED%20%E2%80%94%20open%20for%20claim-orange.svg)](#认领方式)
[![Domain: met](https://img.shields.io/badge/domain-met-2ea44f.svg)](domain.manifest.json)
[![MCP Checker](https://img.shields.io/badge/checker-MCP%20tool-8a2be2.svg)](checker.io.json)

> **状态：RESERVED（占位 · 开放认领）** — 本仓已按平台协议规范建好行业接入四件套骨架，
> 等待具备本域资质的运营方认领并填充真实规则。

把「这块表要不要强检、检了没有、还有效吗」拆成可核验的属性，让 AI 只做状态提示，不做合格结论。

**English:** This repository is the SynomosAI *domain profile* for mandatory metrological verification of measuring instruments. It is a declarative, machine-readable skeleton (manifest / gate-map / privacy / checker contract) — not a runnable product. The embedded checker MCP tool only **shows and verifies provenance** (who issued the certificate, whether it is in date, where the evidence is); it never outputs "pass / fail / safe-to-use" conclusions.

## 这个域管什么

强制检定计量器具周期与合规状态核验

强制检定工作计量器具须周期检定，超期使用在贸易结算、安全防护、医疗卫生、环境监测四类场景中后果直接。AI 能做的是把器具属性与检定状态摆清楚，合格与否以检定证书为唯一依据。

## 域标识

| 项 | 值 |
|---|---|
| 域 ID | `met`（全局唯一，一经分配不复用） |
| 域名称 | 计量 · 强制检定与计量器具合规 |
| Profile 版本 | `domain/1.0` |
| 当前状态 | `RESERVED` |
| 占位时间 | 2026-09-12 |

## 属性清单

| 属性键 | 类型 | 说明 |
|---|---|---|
| `met.instrument_category` | enum | 是否列入强制检定工作计量器具目录，且用于贸易结算/安全防护/医疗卫生/环境监测 · 取值 mandatory/non_mandatory/exempt/undetermined |
| `met.verification_status` | enum | 检定/校准当前状态，以检定证书或检定结果通知书为准 · 取值 verified/overdue/exempt/none/unknown |
| `met.cycle_validity` | enum | 距下次检定到期的剩余时间，用作提醒而非判定 · 取值 within/approaching/expired |
| `met.type_approval` | enum | 计量器具型式批准（CPA）情况 · 取值 approved/not_required/none/unknown |
| `met.verification_body` | string | 执行检定的法定计量检定机构或授权机构名称 |

## 本域红线（不可逾越，机器可读）

1. 不得输出「计量合格」「可继续使用」结论，以检定证书为唯一依据
2. 超过检定周期的强制检定器具不得给出可使用的表述
3. 不得建议调整器具、停用记录或规避强制检定

> 红线在 `gate-map.json` 中均有对应阻断规则。平台校验器会检查「每条红线都有规则覆盖」，
> 缺失即校验失败——**制度与系统不允许不同步**。

## 行业接入四件套

| 文件 | 作用 |
|---|---|
| `domain.manifest.json` | 本域声明：属性清单、签发方要求、有效期、红线 |
| `gate-map.json` | 本域「什么动作要多少摩擦」：silent / warn / confirm / block / require-owner |
| `privacy.json` | 本域隐私声明：默认关闭、最小必要、可撤回、可删除 |
| `checker` | 本域核验器（MCP 工具，**只出示核验，不下判定**） |

## 核心原则

**平台只当擂台，不当货架。** 本域的核验器只回答「这条声明是否可核验、缺什么要件」，
不回答「这件事是否合规、该不该做」。判定权在本域的资质方、监管方与人。

**隐私是准入条件，不是整改事项。** 缺失 `privacy.json` 或任一必填字段不符，
符合性校验直接失败——不是警告，是拒绝接入。

## 🚀 快速开始 / Quick Start

> 本仓为声明式域骨架，**无需安装、无需运行时**；认领方填注 `operator` 与 `checker_endpoint` 后才接入平台校验器。

```bash
# 1. 克隆本仓
git clone https://github.com/SynomosAI/met-mandatory-verify-checker.git
cd met-mandatory-verify-checker

# 2. 通读四件套（纯声明文件，无依赖）
cat domain.manifest.json   # 属性清单 / 签发方要求 / 红线 / 有效期
cat gate-map.json          # 动作摩擦五级：silent/warn/confirm/block/require-owner
cat privacy.json           # 隐私声明（缺失或字段不符 → 校验直接失败）
cat checker.io.json        # MCP 核验器输入输出契约
```

核验器（认领方实现后）的一次典型输入，见 `checker.io.json` 的示例：

```json
{
  "subject_ref": "mpp:cn:9f2c1a7b",
  "attribute_keys": ["met.instrument_category", "met.verification_status"],
  "evidence": ["tsa:2026-09-12#sha256:3b1f..."],
  "request_id": "req-20260912-0001"
}
```

返回结果只会是 `shown / missing / expired / unverifiable` 四态之一，**绝无**「计量合格 / 可继续使用 / 已合规」类结论（见 `checker.io.json` 的 `_forbidden_outputs`）。

## 📁 仓库结构 / Repository layout

```text
met-mandatory-verify-checker/
├── domain.manifest.json   # 域声明：属性 / 签发方要求 / 红线 / 有效期（domain/1.0）
├── gate-map.json          # 动作摩擦五级表（gate/1.0），每条红线必须有对应规则
├── privacy.json           # 隐私声明（privacy/1.0），准入条件
├── checker.io.json        # MCP 核验器输入输出契约（只出示核验，不下判定）
├── CODEOWNERS             # @SynomosAI/synomosai-gov
├── LICENSE                # 代码与配置：MIT
├── LICENSE-CONTENT        # 文档知识版权：All Rights Reserved（可注明出处引用）
├── SECURITY.md            # 安全策略
├── AGENTS.md              # 给 AI Agent 的工作指引
├── llms.txt               # 给 AI 的最小索引
├── llms-full.txt          # 给 AI 的完整文本
└── README.en.md           # English README
```

## 参考依据

- 中华人民共和国计量法
- 中华人民共和国计量法实施细则
- 强制检定的工作计量器具目录
- 计量器具新产品管理办法
- JJF 1033 计量标准考核规范

> 上列依据仅用于说明本域属性的来源与口径，不构成法律意见。具体适用以现行有效文本与主管部门解释为准。

## 认领方式

本域面向具备相应资质的机构开放。认领后请：

1. Fork 本仓，填注 `operator` 与 `checker_endpoint`
2. 按本域现行有效规则校准属性取值与红线表述
3. 跑平台侧校验器自测（五项判据全过方可提交）
4. 提 PR，附资质证明与规则依据

## 许可与署名

代码与配置按 MIT 许可使用。文档的知识版权归 SynomosAI 所有。

© 2026 SynomosAI. All rights reserved.

完整条款：代码与配置见 [LICENSE](LICENSE)（MIT）；文档知识版权见 [LICENSE-CONTENT](LICENSE-CONTENT)（All Rights Reserved，学术/新闻/评估引用请注明出处与条目）。安全问题请私下联系 [SECURITY.md](SECURITY.md)。
