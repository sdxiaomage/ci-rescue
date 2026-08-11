---
title: Case study — safe retries for Electron downloads
description: A public GitHub Actions case study: classify transient Electron download failures without retrying TLS, authentication, configuration, or signal failures
lang: en
---

# Case study: safe retries for Electron downloads

This public contribution fixed a real CI reliability problem in [maka-agent/maka-agent PR #2594](https://github.com/maka-agent/maka-agent/pull/2594). It was approved and merged after one consolidated review revision, with 12 repository checks passing.

This was an unpaid open-source contribution, not a customer engagement or testimonial. It is shown as verifiable delivery evidence only.

[中文案例](https://sdxiaomage.github.io/ci-rescue/zh/cases/maka-electron-retry/)

## The failure

Electron artifact downloads could fail during CI because of temporary network, rate-limit, or upstream server errors. A useful fix needed bounded retries, but it also had to preserve fast failure for permanent problems such as expired TLS certificates, authentication failures, invalid configuration, missing files, and process cancellation.

The original environment also had two distinct caches: npm dependencies and Electron artifacts. Restoring only the dependency cache did not reliably reuse the downloaded Electron binary.

## The first implementation and review

The first implementation added three bounded attempts and exponential backoff. Maintainer review identified two important boundary problems:

1. forcing the Electron downloader's debug namespace could expose a private mirror URL, credentials, or query token in CI logs;
2. matching the top-level text `fetch failed` was too broad because Node may hide a permanent TLS cause underneath that generic message.

The review also suggested preserving the child-process signal instead of converting every signal termination to a generic exit code.

## The accepted design

The consolidated revision moved classification to the actual fetch boundary:

- a small launcher wraps the global Fetch API used by the locked `@electron/get@5.0.0` downloader;
- it walks the error cause chain and emits only a validated code or HTTP status through a private file descriptor;
- it never sends URLs, headers, credentials, query strings, or response bodies through that channel;
- the parent process retries only a strict transport/status allowlist;
- TLS, authentication, malformed context, configuration/DNS failures, spawn failures, and signal termination remain immediate;
- the workflow restores the Electron artifact cache separately from npm's cache.

The production wrapper never scans stderr for retry decisions, so a URL or error message containing a string such as `ECONNRESET` cannot trigger a false retry.

## Verification

The final revision added focused tests for transient codes and HTTP statuses, permanent failures, malformed context, signal handling, debug inheritance, URL-bearing stderr, and the launcher-to-parent structured channel. The final PR changed five files, with most additions in the implementation and its tests.

Evidence:

- [full pull request and discussion](https://github.com/maka-agent/maka-agent/pull/2594);
- [final revision commit](https://github.com/maka-agent/maka-agent/commit/5696c35587561ca7e66c2f70adf3ab572184bd0b);
- [merged commit](https://github.com/maka-agent/maka-agent/commit/88fe9e81d90fee731d726634347432ede011c56c);
- 12 successful repository checks before merge;
- maintainer approval confirmed the production downloader used the intercepted Fetch path and reported no remaining P0–P3 issue.

## Result

The PR was approved at 2026-08-09 18:02:27 UTC and merged two seconds later. The result was not merely “retry more”: it retried a narrow set of failures while keeping secrets out of the new diagnostic channel and preserving fail-fast behavior elsewhere.

## Need a similar repair?

The [CI-S1 package](https://sdxiaomage.github.io/ci-rescue/) is US$39 internationally or CNY ¥299 for mainland-China Alipay payment: one public repository, one failing workflow or root cause, and changes to at most three workflow/configuration files. Diagnosis is free, communication is asynchronous text only, one consolidated revision is included, and payment is due only after acceptance.

[Open a free diagnosis request](https://github.com/sdxiaomage/ci-rescue/issues/new?template=repair-request.yml)
