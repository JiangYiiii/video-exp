---
title: wan2.x/wanx2.1 老接口吃 input.img_url 不是 media 数组，bvid 一度发不出最便宜的 0.1 元探测档
skill: video-editor
schema: video-exp/1
tags: bvid, wan2.2-i2v-flash, img_url, media数组, 老接口, 探测层, 成本, InvalidParameter
severity: medium
date: 2026-11-23
---

## 场景

要生成一段驱动素材，按 `bailian-video` 的成本纪律选最便宜的探测档
`wan2.2-i2v-flash`（0.10 元/秒），`bvid gen --model wan2.2-i2v-flash --first <图>`，
提交即失败。

## 结论

**百炼的图生视频有两套入参形态，按模型分流：**

| 模型 | 首帧怎么传 |
|---|---|
| `wan3.0-video` / `wan3.0-video-prime` | `input.media` 数组，`{"type":"first_frame","url":...}` |
| **`wan2.2-i2v-flash` / `wan2.2-i2v-plus` / `wanx2.1-i2v-turbo`** | **`input.img_url`（单张字符串）** |

`bvid gen` 原来对所有模型都发 `media` 数组，所以**最便宜的 0.10 元/秒探测档
一直是完全不可用的**——而成本纪律恰恰要求用它起步，等于纪律写了但执行不了。
2026-11 已按模型前缀分流修掉；给老接口模型传多素材时 CLI 会警告并只取 `--first`。

推论：**成本纪律类的约定要定期真跑一次。** 这个 bug 能潜伏这么久，
就是因为大家都知道「该用便宜档探测」，但真做事时嫌麻烦直接上了 wan3.0，
没人去验那条便宜路还通不通。

## 证据

同一张 1728×2304 的首帧图、同一条 prompt：

- `--model wan2.2-i2v-flash` 走 `media` 数组 → `task_status: FAILED`，
  `code: InvalidParameter`，`message: img_url must be set for image to video method`
  **失败不计费**
- 改发 `input.img_url` 后 → `SUCCEEDED`，480P/5s 约 15 秒出片，0.50 元

对照：`wan3.0-video` 发 `media` 数组一直正常，所以问题不是 `media` 格式写错，
是老接口根本不认这个字段。

## 反例

- ❌ 报 `img_url must be set` 就去怀疑图片有问题——图没问题，是字段名不对
- ❌ 给老接口模型传多张参考图——它们只吃单张首帧，做不了多角色一致性，
      那是 `wan3.0` 独有的（另见 wan3-first-frame-vs-reference-exclusive）
- ❌ 探测一次失败就放弃便宜档直接上贵的——失败不计费，先看清错误码再决定
