---
title: 案例 — 安全重试 Electron 下载故障
description: 真实 GitHub Actions 合并案例：只重试瞬态 Electron 下载故障，TLS、认证、配置和信号终止保持立即失败
lang: zh-CN
---

# 案例：只重试瞬态 Electron 下载故障

这是一个已经公开合并的真实 CI 修复：[maka-agent/maka-agent PR #2594](https://github.com/maka-agent/maka-agent/pull/2594)。项目在一次合并评审修订后获维护者批准，合并前 12 项仓库检查全部通过。

该项目是无偿开源贡献，不是付费客户案例或客户评价。这里仅把它作为可核验的交付能力证据。

[English case study](https://sdxiaomage.github.io/ci-rescue/cases/maka-electron-retry/)

## 故障是什么

CI 下载 Electron 构件时，可能因临时网络错误、限流或上游 5xx 失败。合理修复需要有限次数重试，同时必须让过期证书、认证失败、错误配置、文件不存在和进程取消等永久问题立即失败。

环境中还存在两类独立缓存：npm 依赖缓存和 Electron 构件缓存。只恢复依赖缓存，不能可靠复用已下载的 Electron 二进制文件。

## 第一版与评审发现

第一版增加了最多三次尝试和指数退避。维护者评审指出两个真实边界问题：

1. 强制开启 Electron 下载器调试命名空间，可能把私有镜像 URL、凭证或查询 token 写进 CI 日志；
2. 只匹配顶层 `fetch failed` 文本过于宽泛，因为 Node 可能把永久 TLS 原因隐藏在这个通用错误下面。

评审还建议保留子进程终止信号，不要把所有信号终止都转换成普通退出码。

## 最终被接受的设计

唯一一次合并修订把错误分类移动到真实 Fetch 边界：

- 独立 launcher 包装锁定版本 `@electron/get@5.0.0` 实际使用的全局 Fetch API；
- 沿错误 cause 链提取经过格式校验的错误码或 HTTP 状态；
- 只通过私有文件描述符传递结构化状态，不传 URL、header、凭证、查询参数或响应正文；
- 父进程只对白名单内的传输错误和 HTTP 状态重试；
- TLS、认证、异常上下文、配置/DNS、进程启动失败和信号终止全部立即失败；
- Electron 构件缓存与 npm 缓存分开恢复。

重试判断不读取 stderr，因此即使 URL 或错误正文包含 `ECONNRESET` 等字符串，也不能伪造一次重试。

## 验证证据

最终修订覆盖瞬态错误、HTTP 状态、永久错误、异常上下文、信号处理、DEBUG 继承、包含 URL 的 stderr，以及 launcher 到父进程的结构化通道。PR 共修改 5 个文件，主要新增内容是实现与定向测试。

- [完整 PR、评审与检查](https://github.com/maka-agent/maka-agent/pull/2594)
- [最终修订提交](https://github.com/maka-agent/maka-agent/commit/5696c35587561ca7e66c2f70adf3ab572184bd0b)
- [合并提交](https://github.com/maka-agent/maka-agent/commit/88fe9e81d90fee731d726634347432ede011c56c)
- 合并前 12 项仓库检查全部成功
- 维护者确认真实生产下载路径确实经过被包装的 Fetch，并批准合并且未发现剩余 P0–P3 问题

## 最终结果

维护者在北京时间 2026-08-10 02:02:27 批准，2 秒后合并。结果不是笼统地“多重试几次”，而是在不扩大日志泄漏和错误恢复风险的前提下，只重试有合理恢复机会的故障。

## 需要类似修复？

[CI-S1 固定套餐](https://sdxiaomage.github.io/ci-rescue/zh/)为大陆 ¥299（支付宝）/ 国际 US$39：1 个公开仓库、1 个失败工作流或单一根因、最多修改 3 个 workflow/配置文件。首次诊断免费，全程异步文字，包含一次合并反馈，修复验收后付款。

[免费提交诊断工单](https://github.com/sdxiaomage/ci-rescue/issues/new?template=repair-request.yml)
