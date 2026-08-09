# CI Rescue — fix your broken GitHub Actions

[中文服务页](https://sdxiaomage.github.io/ci-rescue/zh/) · [English service page](https://sdxiaomage.github.io/ci-rescue/)

Your pipeline is red. I diagnose it for free and use a published fixed package when the failure fits its scope.

## The offer

- **Free first diagnosis** from logs and workflow files.
- **CI-S1 fixed price: US$39** — one public repository, one failing workflow or root cause, and changes to at most three workflow/configuration files.
- **Pay only after acceptance** — no fee if I cannot produce an accepted fix.
- **Async only** — no calls, meetings, group chats, or live screen sharing.
- **One consolidated written revision** within the agreed scope; no open-ended negotiation.
- A pull request with the fix, a short root-cause explanation, and verification notes.
- No secrets, production access, or repository ownership required.

Good fits include dependency/cache failures, test or lint jobs that pass locally but fail in CI, matrix/version problems, permissions errors, flaky steps, release-workflow regressions, and Windows/Linux path differences.

CI-S1 excludes business-feature development, private production access, secrets, migrations, and unrelated failures. Acceptance means the agreed failing job passes under the same trigger, or a written substitute test agreed before coding passes. Out-of-scope work is declined or handled as a separate published package; it is not negotiated live.

Run the free, zero-dependency [CI Health Audit](https://github.com/sdxiaomage/ci-health-audit) first. It checks common GitHub Actions security and reliability risks and produces a Markdown or JSON report. The audit is self-service; no contact is required.

For fixed-template Excel/CSV cleanup, exception reporting, and reconciliation, see the [public automation demo](https://github.com/sdxiaomage/excel-automation-demo). The fixed packages are ¥399 and ¥700, async text only, with one consolidated revision.

Not accepted: credential recovery, bypassing access controls, malware, spam, fake reviews, academic cheating, or work that requires handling private production data.

## Start here

[Open a repair request](../../issues/new?template=repair-request.yml). Include the repository URL, failing run URL, expected behavior, and any private constraints. Public logs are best; redact secrets before posting.

I will reply with one of three outcomes:

1. a free answer when the fix is obvious;
2. a fixed-price proposal with scope and acceptance test; or
3. a clear decline if the job is unsafe, underspecified, or outside scope.

No payment is requested until a fix has been delivered and accepted. Payment method is agreed in writing before work begins; never send credentials or money through an unsolicited message.

The listed package has a fixed scope and price. After delivery, the client may send one consolidated asynchronous written feedback message. I submit the revision once, then wait for acceptance and payment. Requests that require meetings, group chats, repeated negotiation, or ongoing synchronous support are declined.

## Payment after acceptance / 验收后付款

Alipay is available for domestic clients. Use this code only after the written scope and price are agreed and the repair has been accepted. Payment is never required merely to open a diagnosis request.

国内客户可在双方书面确认工作范围和价格、修复结果验收后，使用以下支付宝收款码付款。仅提交免费诊断工单不需要付款。

<img src="assets/alipay-receive.jpg" alt="Alipay receiving code / 支付宝收款码" width="420">

---

# CI 急救 — 修复 GitHub Actions / CI

流水线报错时，我先免费查看日志和工作流文件；符合公开 CI-S1 范围时按公示固定价执行。

可以先自行运行免费的 [CI Health Audit](https://github.com/sdxiaomage/ci-health-audit)，扫描常见的 GitHub Actions 安全与可靠性风险并生成报告，无需联系或沟通。

- 首次诊断免费；
- **CI-S1 固定价 US$39**：1 个公开仓库、1 个失败工作流或单一根因、最多修改 3 个 workflow/配置文件；
- 修复被验收后才付款，无法交付可验收修复则不收费；
- 全程异步文字沟通，不拉群、不开会、不语音或视频；
- 固定价格、固定范围，不议价；最多接受一次合并后的异步文字反馈；
- 交付物包括修复 PR、根因说明和验证记录；
- 不需要仓库所有权、生产权限或任何密钥。

[提交修复工单](../../issues/new?template=repair-request.yml)。请附仓库地址、失败运行地址、预期结果，并在发布前删除日志里的密钥和隐私信息。

这不是“保证赚钱”或投资项目，而是一项按结果收费的技术服务。
